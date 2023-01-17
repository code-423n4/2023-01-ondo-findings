
## G-01 Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used. It saves **30-40 gas** per loop.

_There are 9 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

750: for (uint256 i = 0; i < size; ++i) {
786: for (uint256 i = 0; i < size; ++i) {
933: for (uint256 i = 0; i < size; ++i) {
961: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
File: contracts/cash/factory/CashFactory.sol

127: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
File: contracts/cash/factory/CashKYCSenderFactory.sol

137: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

137: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
File: contracts/cash/kyc/KYCRegistry.sol

163: for (uint256 i = 0; i < length; i++) {
180: for (uint256 i = 0; i < length; i++) {
```

---------------

## G-02 x += y costs more gas than x = x + y for state variables (same applies for x -= y)

Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**.

_There are 9 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

221: mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
582: currentEpoch += epochDifference;
630: currentMintAmount += collateralAmountIn;
649: currentRedeemAmount += amount;
678-680: redemptionInfoPerEpoch[currentEpoch].addressToBurnAmt[
        msg.sender
      ] += amountCashToRedeem;
681: redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
792: totalCashAmountRefunded += cashAmountBurned;
865: redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
867: redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```

-----

## G-03 Splitting require() statements that use && saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
File: contracts/lending/OndoPriceOracleV2.sol

292-296: require(
      (answeredInRound >= roundId) &&//@audit-info Splitting require() statements that use && saves gas
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
File: contracts/lending/tokens/cCash/CTokenCash.sol

45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
File: contracts/lending/tokens/cToken/CTokenModified.sol

45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```

---------

## G-04 Internal functions only called once can be inlined to save gas

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 12 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
File: contracts/lending/OndoPriceOracle.sol

119: function _setFTokenToCToken(address fToken, address cToken) internal {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
File: contracts/lending/OndoPriceOracleV2.sol

210: function _setFTokenToCToken(address fToken, address cToken) internal {
251: function _setFTokenToChainlinkOracle(
324: function _min(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
File: contracts/lending/tokens/cCash/CTokenCash.sol

679: function borrowFresh(address payable borrower, uint borrowAmount) internal {
870: function liquidateBorrowFresh(
1151: function _setReserveFactorFresh(
1198: function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
1253: function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
File: contracts/lending/tokens/cToken/CTokenModified.sol

491: function mintFresh(address minter, uint mintAmount) internal {
1201: function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
1256: function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
```

-----------

## G-05 Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

The expression is re-calculated each time the constant is referenced. Each usage of a "constant" costs ~**100gas** more on each access. You could use `immutable` , then you only pay the gas costs for the computation at deploy time.

_There are 14 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

122: bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
123: bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
124: bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
File: contracts/cash/factory/CashFactory.sol

44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
File: contracts/cash/factory/CashKYCSenderFactory.sol

44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
File: contracts/cash/kyc/KYCRegistry.sol

32-33: keccak256(
      "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
File: contracts/cash/token/Cash.sol

22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
File: contracts/cash/token/CashKYCSender.sol

27: keccak256("KYC_CONFIGURER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
File: contracts/cash/token/CashKYCSenderReceiver.sol

27: keccak256("KYC_CONFIGURER_ROLE");
```

--------

## G-06 Require() or revert() strings longer than 32 bytes cost extra gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

_There are 43 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
File: contracts/cash/factory/CashFactory.sol

152: require(msg.sender == guardian, "CashFactory: You are not the Guardian");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
File: contracts/cash/factory/CashKYCSenderFactory.sol

163-165: require(
      msg.sender == guardian,
      "CashKYCSenderFactory: You are not the Guardian"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

163-165: require(
      msg.sender == guardian,
      "CashKYCSenderReceiverFactory: You are not the Guardian"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
File: contracts/cash/kyc/KYCRegistry.sol

87: require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");

88-90: require(
      !kycState[kycRequirementGroup][user],
      "KYCRegistry: user already verified"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
File: contracts/cash/token/Cash.sol

36-38: require(
      hasRole(TRANSFER_ROLE, _msgSender()),
      "Cash: must have TRANSFER_ROLE to transfer"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
File: contracts/cash/token/CashKYCSender.sol

70-72: require(
        _getKYCStatus(from),
        "CashKYCSender: `from` address must be KYC'd to send tokens"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
File: contracts/cash/token/CashKYCSenderReceiver.sol

63-65: require(
      _getKYCStatus(_msgSender()),
      "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
      
70-72: require(
        _getKYCStatus(from),
        "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
        
78-80: require(
        _getKYCStatus(to),
        "CashKYCSenderReceiver: `to` address must be KYC'd to receive tokens"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```
File: contracts/lending/JumpRateModelV2.sol

89: require(msg.sender == owner, "only the owner may call this function.");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
File: contracts/lending/OndoPriceOracle.sol

120-122: require(
      CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
      "cToken and fToken must have the same underlying asset if cToken nonzero"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
File: contracts/lending/OndoPriceOracleV2.sol

215-217: require(
      CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
      "cToken and fToken must have the same underlying asset"
      
280-282: require(
      fTokenToOracleType[fToken] == OracleType.CHAINLINK,
      "fToken is not configured for Chainlink oracle"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```
File: contracts/lending/tokens/cErc20ModifiedDelegator.sol

725-727: require(
      msg.sender == admin,
      "CErc20Delegator::_setImplementation: Caller must be admin"
      
1251-1253: require(
      msg.value == 0,
      "CErc20Delegator:fallback: cannot send value to fallback"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```
File: contracts/lending/tokens/cCash/CCash.sol

151-153: require(
      msg.sender == admin,
      "cErc20::sweepToken: only admin can sweep tokens"
      
155-157: require(
      address(token) != underlying,
      "cErc20::sweepToken: can not sweep underlying token"
      
269-271: require(
      msg.sender == admin,
      "only the admin may set the comp-like delegate"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol

```
File: contracts/lending/tokens/cCash/CCashDelegate.sol

30-32: require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"

45-47: require(
      msg.sender == admin,
      "only the admin may call _resignImplementation"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
File: contracts/lending/tokens/cCash/CTokenCash.sol

44: require(msg.sender == admin, "only admin may initialize the market");

45-47: require(
      accrualBlockNumber == 0 && borrowIndex == 0, 
      "market may only be initialized once"

52-54: require(
      initialExchangeRateMantissa > 0,
      "initial exchange rate must be greater than zero."

67: require(err == NO_ERROR, "setting interest rate model failed");

584-586: require(
      redeemTokensIn == 0 || redeemAmountIn == 0,
      "one of redeemTokensIn or redeemAmountIn must be zero"

931-933: require(
      amountSeizeError == NO_ERROR,
      "LIQUIDATE_COMPTROLLER_CALCULATE_AMOUNT_SEIZE_FAILED"

1357: require(msg.sender == admin, "Only admin can set KYC requirement group");

1389: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```
File: contracts/lending/tokens/cToken/CErc20.sol

151-153: require(
      msg.sender == admin,
      "cErc20::sweepToken: only admin can sweep tokens"

155-157: require(
      address(token) != underlying,
      "cErc20::sweepToken: can not sweep underlying token"

269-271: require(
      msg.sender == admin,
      "only the admin may set the comp-like delegate"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol

```
File: contracts/lending/tokens/cToken/CTokenDelegate.sol

30-32: require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"

45-47: require(
      msg.sender == admin,
      "only the admin may call _resignImplementation"
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
File: contracts/lending/tokens/cToken/CTokenModified.sol

44: require(msg.sender == admin, "only admin may initialize the market");

45-47: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"

52-54: require(
      initialExchangeRateMantissa > 0,
      "initial exchange rate must be greater than zero."

67: require(err == NO_ERROR, "setting interest rate model failed");

584-586: require(
      redeemTokensIn == 0 || redeemAmountIn == 0,
      "one of redeemTokensIn or redeemAmountIn must be zero"

931-933: require(
      amountSeizeError == NO_ERROR,
      "LIQUIDATE_COMPTROLLER_CALCULATE_AMOUNT_SEIZE_FAILED"

1360: require(msg.sender == admin, "Only admin can set KYC requirement group");

1392: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```

----

## G-07 Not using the named return variables when a function returns, wastes deployment gas

_There is 1 instance of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

781-784: function _processRefund(
    address[] calldata refundees,
    uint256 epochToService
  ) private returns (uint256 totalCashAmountRefunded) {
```

-----

## G-08 Use msg.sender instead of openzeppelin's _msgsender()_ when meta-transactions capabailities aren't used

`msg.sender` costs **2 gas** (CALLER opcode).
`_msgSender()` represents the following:

```
function _msgSender() internal view virtual returns (address payable) {  return msg.sender;}
```

When no meta-transactions capabilities are used: `msg.sender` is enough. 
Consider replacing `_msgSender()` with `msg.sender` here

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
File: contracts/cash/token/Cash.sol

37: hasRole(TRANSFER_ROLE, _msgSender()),
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
File: contracts/cash/token/CashKYCSender.sol

64: _getKYCStatus(_msgSender()),
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
File: contracts/cash/token/CashKYCSenderReceiver.sol

64: _getKYCStatus(_msgSender()),
```

---------

