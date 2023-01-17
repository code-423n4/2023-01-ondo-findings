### [G-01] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per &):

**contracts\lending\OndoPriceOracleV2.sol**

292:` require(
      (answeredInRound >= roundId) && //
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );`

**contracts\lending\tokens\cCash\CTokenCash.sol**

45:` require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );`

**contracts\lending\tokens\cToken\CTokenModified.sol**

45 :` require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );`

### [G-02] USE NAMED RETURNS WHERE APPROPRIATE

**contracts\lending\OndoPriceOracleV2.sol**

94: ` external view override returns (uint256)`

**contracts\lending\tokens\cCash\CTokenCash.sol**

771:  ` internal returns (uint)`

**contracts\lending\tokens\cToken\CTokenModified.sol**

771:` internal returns (uint)`



### [G-03] REQUIRE() OR REVERT() STATEMENTS THAT CHECK INPUT ARGUMENTS SHOULD BE AT THE TOP OF THE FUNCTION
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting gas in a function that may ultimately revert in the unhappy case.

**contracts\cash\token\CashKYCSenderReceiver.sol**

56:` function _beforeTokenTransfer(
    address from,
    address to,
    uint256 amount
  ) internal override {
    super._beforeTokenTransfer(from, to, amount);
    require(
      _getKYCStatus(_msgSender()),
      "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
    );`

    if (from != address(0)) {
      // Only check KYC if not minting
      require(
        _getKYCStatus(from),
        "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
      );
    }

    if (to != address(0)) {
      // Only check KYC if not burning
      require(
        _getKYCStatus(to),
        "CashKYCSenderReceiver: `to` address must be KYC'd to receive tokens"
      );
    }
  }`
### [G-04] State variables should be cached in stack variables rather than re-reading them from storage

**contracts\lending\tokens\cToken\CTokenModified.sol**

` borrowSnapshot.principal` in function borrowBalanceStoredInternal():

323:` if (borrowSnapshot.principal == 0) {`

330: ' uint principalTimesIndex = borrowSnapshot.principal * borrowIndex;`

### [G-05] Parameters of function should be checked first to avoid waste of gas:

**contracts\lending\tokens\cToken\CTokenModified.sol**

In this function we should check msg.sender first, then 

   ` if (reduceAmount > totalReserves) {
      revert ReduceReservesCashValidation();
    }`


1256 : ` function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
    uint totalReservesNew;
    if (msg.sender != admin) {
      revert ReduceReservesAdminCheck();
    }`

   ` if (accrualBlockNumber != getBlockNumber()) {
      revert ReduceReservesFreshCheck();
    }`

    if (getCashPrior() < reduceAmount) {
      revert ReduceReservesCashNotAvailable();
    }

    if (reduceAmount > totalReserves) {
      revert ReduceReservesCashValidation();
    }`

The same in function _setReserveFactorFresh()

After checking msg.sender we check newReserveFactorMantissa:

    ` if (newReserveFactorMantissa > reserveFactorMaxMantissa) {
      revert SetReserveFactorBoundsCheck();
    }`


1154: ` function _setReserveFactorFresh(
    uint newReserveFactorMantissa
  ) internal returns (uint) {
    if (msg.sender != admin) {
      revert SetReserveFactorAdminCheck();
    }

    if (accrualBlockNumber != getBlockNumber()) {
      revert SetReserveFactorFreshCheck();
    }

    if (newReserveFactorMantissa > reserveFactorMaxMantissa) {
      revert SetReserveFactorBoundsCheck();
    }`

### [G-06]  pendingAdmin = payable(address(0)); consumes more gas than  pendingAdmin = address(0);

**contracts\lending\tokens\cToken\CTokenModified.sol**

When you use pendingAdmin = payable(address(0)); you are converting the address(0) to a payable address, which can be used to receive ether, but this is not necessary in this case because address(0) is already a valid address, so no conversion is needed.

On the other hand, when you use pendingAdmin = address(0); you are simply assigning the address(0) to the variable, and this is less expensive in terms of gas usage because it doesn't require any additional computation.

Therefore, using pendingAdmin = address(0); is more efficient in terms of gas usage, as it doesn't require the extra step of type casting the address to a payable address.

1101:`  // Clear the pending value
    pendingAdmin = payable(address(0));`