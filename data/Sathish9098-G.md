
# GAS OPTIMIZATIONS

##

###  [GAS-1] ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

[FILE : 2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol)

        127 : for (uint256 i = 0; i < exCallData.length; ++i) {

[FILE : 2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol)

        137:  for (uint256 i = 0; i < exCallData.length; ++i) {

[FILE : 2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol)

        163:  for (uint256 i = 0; i < length; i++) {

        180 :  for (uint256 i = 0; i < length; i++) {

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

       750:  for (uint256 i = 0; i < size; ++i) {

      786:   for (uint256 i = 0; i < size; ++i) {

      933:   for (uint256 i = 0; i < size; ++i) {

      961:   for (uint256 i = 0; i < exCallData.length; ++i) {

## [GAS-9]  USE REQUIRE INSTEAD OF ASSERT

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling

[CashFactory.sol-97](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L97)

[CashKYCSenderFactory.sol-106](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L106)

[CashKYCSenderReceiverFactory.sol-106](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106)

##

### [GAS-2] instead of using operator && on single require  . Using double REQUIRE  checks can save more gas. After splitting require statement its possible save 8 gas

    BEFORE MIGRATION : 

require(
      (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );

Recommended Mitigation Steps

require( answeredInRound >= roundId,"Chainlink oracle price is stale");
require (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),"Chainlink oracle price is stale");

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol)

            require(
            (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
          );

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol] (https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol)


               45: require(
               accrualBlockNumber == 0 && borrowIndex == 0,
              "market may only be initialized once"
               );


##

###  [GAS-3]  ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 30-40 gas

feesInCollateral is not going to higher than collateralAmountIn . So there is no possibility of the overflow/underflow 

> SOLUTION:

  if OR require(a <= b); x = b - a => if  OR require(a <= b); unchecked { x = b - a }

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)


       if (exchangeRate > lastSetMintExchangeRate) {
       297:    rateDifference = exchangeRate - lastSetMintExchangeRate;


       else if (exchangeRate < lastSetMintExchangeRate) {
      299:  rateDifference = lastSetMintExchangeRate - exchangeRate;
   
      210:    uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral;

##

### [GAS-4] CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS.ITS POSSIBLE TO SAVE 10 GAS FOR EVERY ITERATIONS AS PER REMIX REPORTS 

Make it outside and only use it inside.

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

         786:  for (uint256 i = 0; i < size; ++i) {

       // variable declared inside the loop
        address refundee = refundees[i];  
        uint256 cashAmountBurned = redemptionInfoPerEpoch[epochToService]
        .addressToBurnAmt[refundee];


       redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;
       cash.mint(refundee, cashAmountBurned);
       totalCashAmountRefunded += cashAmountBurned;
       emit RefundIssued(refundee, cashAmountBurned, epochToService);
       }     


      750:   for (uint256 i = 0; i < size; ++i) {

      address redeemer = redeemers[i];
      uint256 cashAmountReturned = redemptionInfoPerEpoch[epochToService]
        .addressToBurnAmt[redeemer];

      redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;
      uint256 collateralAmountDue = (amountToDist * cashAmountReturned) /
        quantityBurned;  

##

### [GAS-5]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

     function _processRefund(
    address[] calldata refundees,
    uint256 epochToService
    ) private returns (uint256 totalCashAmountRefunded) {


##

### [GAS-6]  MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

If both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key???s keccak256 hash (Gkeccak256 - 30 gas) and that calculation???s associated stack operations

fTokenToUnderlyingPrice and fTokenToCToken are both being used in the same functions mostly consider making them a struct instead

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol)

          45 : mapping(address => uint256) public fTokenToUnderlyingPrice;

          49 : mapping(address => address) public fTokenToCToken;

##

### [GAS-7] Use uint256 Instead uint8 in the function parameters. 


Such as uint8, are only more effective when multiple variables can be stored in the same storage space, like in structs. Uint256 uses less gas than uint8 in loops and other situations.

[FILE : 2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol)

          83:   uint8 v,

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol ](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol)

         32 :   uint8 public decimals;


       
### [GAS-8]  USE FUNCTION INSTEAD OF MODIFIERS

[FILE : 2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol)

          modifier onlyGuardian() {
          require(msg.sender == guardian, "CashFactory: You are not the Guardian");
          _;
         }

[FILE : 2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol)

     modifier onlyGuardian() {
    require(
      msg.sender == guardian,
      "CashKYCSenderFactory: You are not the Guardian"
    );
    _;
    }

##

































GAS-1	Use assembly to check for address(0)	9
GAS-2	array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)	8
GAS-3	Using bools for storage incurs overhead	7
GAS-4	Cache array length outside of loop	4
GAS-5	State variables should be cached in stack variables rather than re-reading them from storage	20
GAS-6	Use calldata instead of memory for function arguments that do not get mutated	19
GAS-7	Use Custom Errors	49
GAS-8	Don't initialize variables with default value	14
GAS-9	Long revert strings	11
GAS-10	Functions guaranteed to revert when called by normal users can be marked payable	7
GAS-11	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	2
GAS-12	Using private rather than public for constants, saves gas	27
GAS-13	Use != 0 instead of > 0 for unsigned integer comparison	6
GAS-14	internal functions not called by the contract should be removed	20