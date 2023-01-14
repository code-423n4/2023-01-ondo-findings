# QA REPORT

## [L-1] Use of `block.timestamp`

### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### ✅ Recommendation
Use oracle instead of `block.timestamp`

#### *Instances(7):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L175 )
```solidity
175: block.timestamp -
176: (block.timestamp % epochDuration);
577: uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
584: block.timestamp -
585: (block.timestamp % epochDuration);
```
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L92 )
```solidity
92: require(block.timestamp <= deadline, "KYCRegistry: signature expired");
```
File: [2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L294 )
```solidity
294: (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
```

## [L-2] Use `call()` instead of `transfer()`

### Description
The `transfer()` function only allows the recipient to use 2300 gas. If the recipient uses more than that, transfers will fail. In the future gas costs might change increasing the likelihood of that happening.
Keep in mind that `call()` introduces the risk of reentrancy, but as the code follows the CEI pattern it should be fine.

### ✅ Recommendation
Replace `transfer()` calls with `call()`, keep in mind to check whether the call was successful by validating the return value.

#### *Instances(4):*
File: [2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L160 )
```solidity
160: token.transfer(admin, balance);
241: token.transfer(to, amount);
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L160 )
```solidity
160: token.transfer(admin, balance);
241: token.transfer(to, amount);
```

## [L-3] `require()` should be used instead of `assert()`

### Description
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input.

### ✅ Recommendation
You should change from `assert()` to `require()`

#### *Instances(3):*
File: [2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L97 )
```solidity
97: assert(cashProxyAdmin.owner() == guardian);
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106 )
```solidity
106: assert(cashKYCSenderProxyAdmin.owner() == guardian);
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106 )
```solidity
106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```

## [N-1] For modern and more readable code, update import usages.

### Description
Solidity code is cleaner in the following way: On the principle that clearer code is better code, you should import the things you want to use. Specific imports with curly braces allow us to apply this rule better. Check out this [article](https://betterprogramming.pub/solidity-tutorial-all-about-imports-c65110e41f3a)

### ✅ Recommendation
Import like this: `import {contract1 , contrat2c} from "filename.sol";`

#### *Instances(71):*
All import statements

## [N-2] Compliance with solidity styler rules in `constant` expressions.

### Description
Variables declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format..

### ✅ Recommendation
Conform to the style guide

#### *Instances(16):*
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L31 )
```solidity
31: bytes32 public constant _APPROVAL_TYPEHASH =
```
File: [2023-01-ondo/contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L29 )
```solidity
29: uint public constant blocksPerYear = 2628000;
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35 )
```solidity
35: uint internal constant borrowRateMaxMantissa = 0.0005e16;
38: uint internal constant reserveFactorMaxMantissa = 1e18;
115: uint public constant protocolSeizeShareMantissa = 0; //0%
166: ISanctionsList public constant sanctionsList =
184: bool public constant isCToken = true;
```
File: [2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L208 )
```solidity
208: uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
213: uint256 internal constant reserveFactorMaxMantissa = 1e18;
350: ISanctionsList public constant sanctionsList =
368: bool public constant isCToken = true;
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33 )
```solidity
33: uint internal constant borrowRateMaxMantissa = 0.0005e16;
36: uint internal constant reserveFactorMaxMantissa = 1e18;
113: uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
164: ISanctionsList public constant sanctionsList =
182: bool public constant isCToken = true;
```
