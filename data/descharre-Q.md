# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Other contract addresses can only be set once | 1 |
|2       | Everyone can call initialize() function| 4 |
|3      | type(uint).max is not equal to -1 | 1 |

## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Don't use keccack256 in constants | 8 |
| 2      | Require() or revert() statement that check input arguments should be at the top of the function| 5 |
| 3      | Similar contracts| 4 |
| 4      |Use modifier instead of duplicate require| 5 |
| 5     |Multiple address mappings can be combined into a single mapping of an address to a struct| 1 |
| 6      |Miscellaneous| 1 |


# Details
## Low Risk
## 1 Other contract addresses can only be set once
Other contract addresses are immutable so they can only be set once in the constructor. There is no other setter method for this. 

This makes it so there is no room for error and can lead to contract reverts and redeployment.

A solution for this is to make a setter method which only the owner can call. So if the contract address is wrong it can always be reset.

[CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L163-L164)
```solidity
L163:   collateral = IERC20(_collateral);
L164:   cash = Cash(_cash);
```
## 2 Everyone can call initialize() function
When the contract is not initialized, the initialize() function can be called by anybody. This can be an issue because roles will be set in the __ERC20PresetMinterPauser_init function.
- [CashKYCSender](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
- [CashKYCSenderReceiver](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)
- [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)
- [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol)
## 3 type(uint).max is not equal to -1 
Documentation says fail if the repayAmount is -1. However type(uint).max is not equal to -1. Better use just -1 here.
```solidity
    /* Fail if repayAmount = -1 */
    if (repayAmount == type(uint).max) {
      revert LiquidateCloseAmountIsUintMax();
    }
```
## Non critical
## 1 Don't use keccack256 in constants
Use the literal bytes32 value instead of the keccak256 or other computations

[CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L122-L124)
```solidity
L122:  bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
L123:  bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
L124:  bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
```
Also found in the following contracts:
- [CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L44-L46)
- [CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L44-L46)
- [CashKYCSenderReceiverFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44-L46)
- [KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L31-L36)
- [Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22)
- [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26)
- [CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L26)

## 2 Require() or revert() statement that check input arguments should be at the top of the function
This way no gas is wasted when the function is destined to fail.
- [CashManager.sol#L249-L251](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L249-L251)
- [CashManager.sol#L716-L718](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L716-L718)
- [Cash.sol#L36-L39](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L36-L39)
- [CashKYCSender.sol#L63-L66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L63-L66)
- [CashKYCSenderReceiver.sol#L63-L66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L63-L66)

## 3 Similar contracts
[CashKYCSender](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol) and [CashKYCSenderReceiver](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol) are almost Identical.

It's better to work with an abstract contract here where both of the contracts inherit from. This will save a lot of duplicate code.

The same applies for

[CashKYCSenderFactory](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol) and [CashKYCSenderReceiverFactory](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol)

For the two erc20 contracts [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol) and [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol) there can also be one main contract where both of them inherit from, there are only 4 functions that are different so it will remove a lot of duplicate code. 

Same applies for [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol) and [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol)

## 4 Use modifier instead of duplicate require
- [CTokenCash.sol#L1064](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1064)
- [CTokenCash.sol#L1116](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1116)
- [CTokenCash.sol#L1155](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1155)
- [CTokenCash.sol#L1258](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1258)
- [CTokenCash.sol#L1321](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1321)
if (msg.sender != admin) is called 5 times. It's better to use a modifier instead. It improves readability. One reason why you might have done this is because it reverts a different error message each time. But for this error you can also just a have one error message: revert SENDERISNOTADMIN for example .

Same thing can be done for the statement if (accrualBlockNumber != getBlockNumber()). This is called 9 times

## 5 Multiple address mappings can be combined into a single mapping of an address to a struct
[OndoPriceOracleV2.sol#L55-L73](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L55-L73)

```solidity
  /// @notice fToken to Oracle Type association
  mapping(address => OracleType) public fTokenToOracleType;

  /// @notice Contract storage for fToken's underlying asset prices
  mapping(address => uint256) public fTokenToUnderlyingPrice;

  /// @notice fToken to cToken associations for piggy backing off
  ///         of Compound's Oracle
  mapping(address => address) public fTokenToCToken;

  struct ChainlinkOracleInfo {
    AggregatorV3Interface oracle;
    uint256 scaleFactor;
  }

  /// @notice fToken to Chainlink oracle association
  mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;

  /// @notice Price cap for the underlying asset of an fToken. Optional.
  mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```
Can be changed to
```solidity
  struct tokenInfo{
    OracleType oracleType;
    uint256 underlyingPrice;
    address cToken;
    ChainlinkOracleInfo oracleInfo;
    uint256 underlyingPriceCap;
  }
  /// @notice fToken to Oracle Type association
  mapping(address => tokenInfo) public fTokenToInfo;


  struct ChainlinkOracleInfo {
    AggregatorV3Interface oracle;
    uint256 scaleFactor;
  }
```
## Miscellaneous
### Checking for under or overflow is useless
The evm will revert whenever over or underflow occurs because the contract is using a higher version of solidity than 0.8.0. So I don't see the point of doing this seperately.
[CTokenCash.sol#L121-L130](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L121-L130)

My solution with also a bit of gas saved:
```diff
    /* Do the calculations, checking for {under,over}flow */
    uint allowanceNew = startingAllowance - tokens;
-   uint srcTokensNew = accountTokens[src] - tokens;
-   uint dstTokensNew = accountTokens[dst] + tokens;
+   accountTokens[src] = accountTokens[src] - tokens;
+   accountTokens[dst] = accountTokens[dst] + tokens;
    /////////////////////////
    // EFFECTS & INTERACTIONS
    // (No safe failures beyond this point)

-   accountTokens[src] = srcTokensNew;
-   accountTokens[dst] = dstTokensNew;
```