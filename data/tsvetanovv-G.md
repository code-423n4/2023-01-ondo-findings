## [GAS-01] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE. 
It allows setting contract-level variables at construction time which gets stored in code rather than storage.
```
JumpRateModelV2.sol
24: address public owner;

cErc20ModifiedDelegator.sol
218: ddress payable public admin;
```
***

## [GAS-02] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
Use abi.encodePacked() where possible to save gas.

```
KYCRegistry.sol:
93: bytes32 structHash = keccak256(
      abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```
***

## [GAS-03] Require() or revert() statements that check input arguments should be at the top of the function
```
CErc20.sol:
function sweepToken(EIP20NonStandardInterface token) external override {
    require(
      msg.sender == admin,
      "cErc20::sweepToken: only admin can sweep tokens"
    );
    require(
      address(token) != underlying,
      "cErc20::sweepToken: can not sweep underlying token"
    );
    uint256 balance = token.balanceOf(address(this));
    token.transfer(admin, balance);
  }
```
***

## [GAS-04] USE NAMED RETURNS FOR LOCAL VARIABLES WHERE IT IS POSSIBLE
```
OndoPriceOracleV2.sol:
92: function getUnderlyingPrice(
    address fToken
  ) external view override returns (uint256) {
    uint256 price;

CTokenCash.sol:
771: ) internal returns (uint) {
```
***

## [GAS-05]  UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

```
CashFactory.sol:
127: for (uint256 i = 0; i < exCallData.length; ++i) {

CashKYCSenderFactory.sol:
137: for (uint256 i = 0; i < exCallData.length; ++i) {

CashKYCSenderReceiverFactory.sol:
137: for (uint256 i = 0; i < exCallData.length; ++i) {

KYCRegistry.sol:
163: for (uint256 i = 0; i < length; i++) {
180: for (uint256 i = 0; i < length; i++) {

CashManager.sol:
750: for (uint256 i = 0; i < size; ++i) {
786: for (uint256 i = 0; i < size; ++i) {
933: for (uint256 i = 0; i < size; ++i) {
961: for (uint256 i = 0; i < exCallData.length; ++i) {
```
***

## [GAS-06] SPLITTING REQUIRE() STATEMENTS THAT USE `&&` SAVES GAS
```
OndoPriceOracleV2.sol:
292: require(
      (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );

CTokenCash.sol:
45: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );

CTokenModified.sol:
45: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```
***

## [GAS-07] Should use arguments instead of state variable
```
CashManager.sol:
821: 	minimumRedeemAmount = newRedeemMinimum;
822:    emit MinimumRedeemAmountSet(oldRedeemMin, minimumRedeemAmount);

cErc20ModifiedDelegator.sol:
737: 	implementation = implementation_;
746: 	emit NewImplementation(oldImplementation, implementation);
```