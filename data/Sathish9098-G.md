
# GAS OPTIMIZATIONS

##

## [GAS-1]  MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE

If both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations

fTokenToUnderlyingPrice and fTokenToCToken are both being used in the same functions mostly consider making them a struct instead

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol)

          45 : mapping(address => uint256) public fTokenToUnderlyingPrice;

          49 : mapping(address => address) public fTokenToCToken;

##

## [GAS-2] ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

[FILE : 2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol)

        127 : for (uint256 i = 0; i < exCallData.length; ++i) {

[FILE : 2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol)

        137:  for (uint256 i = 0; i < exCallData.length; ++i) {

[FILE : 2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol)

        163:  for (uint256 i = 0; i < length; i++) {

        180 :  for (uint256 i = 0; i < length; i++) {

##

## [GAS-3]  USE FUNCTION INSTEAD OF MODIFIERS

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

## [GAS-4] Use uint256 Instead uint8 in the function parameters. 


Such as uint8, are only more effective when multiple variables can be stored in the same storage space, like in structs. Uint256 uses less gas than uint8 in loops and other situations.

[FILE : 2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol)

          83:   uint8 v,

##

## [GAS-5] instead of using operator && on single require  . Using double REQUIRE  checks can save more gas. After splitting require statement its possible save 8 gas

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