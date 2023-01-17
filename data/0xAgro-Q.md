# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Long Lines (> 120 Characters)|26|
|[NC-02]|Spelling Mistakes|8|
|[NC-03]|`assert` Used Over `require`|2|
|[NC-04]|Underscore Notation Not Used / Not Used Consistently|2|
|[NC-05]|Named Imports Not Used|All Contracts|
|[NC-06]|Contract Layout Voids Solidity Docs|13 Contracts|
|[NC-07]|Explicit Data Types Not Used Consistently|10 Contracts|
|[NC-08]|Contracts Missing `@title` NatSpec Tag|8 Contracts|
|[NC-09]|Order of Functions Not Compliant With Solidity Docs|7 Contracts|
|[NC-10]|No License Indication|2 Contracts|

### [NC-01 Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*contracts/lending/JumpRateModelV2.sol*
*  [57](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L57).

*contracts/lending/tokens/cCash/CCash.sol*
*  [147](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L147), [191](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L191), [230](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L230), [234](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L234). 

*contracts/lending/tokens/cToken/CErc20.sol*
*  [147](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L147), [191](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L191), [230](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L230), [234](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L234). 

*contracts/lending/OndoPriceOracleV2.sol*
*  [298](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L298).

*contracts/lending/tokens/cErc20ModifiedDelegator.sol*
*  [997](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L997), [1095](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1095). 

*contracts/lending/tokens/cCash/CTokenCash.sol*
*  [287](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L287), [573](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L573), [574](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L574), [1056](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1056), [1196](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1196), [1414](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1414), [1425](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1425). 

*contracts/lending/tokens/cToken/CTokenModified.sol*
*  [287](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L287), [573](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L573), [574](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L574), [1059](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1059), [1199](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1199), [1417](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1417), [1428](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1428).

### [NC-02] Spelling Mistakes

*/contracts/cash/kyc/KYCRegistry.sol*
* `successfully` is misspelled as [`sucessfully`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L62)
* `eligible` is misspelled as [`elligible`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L205)

*/contracts/lending/tokens/cCash/CCash.sol*
* `of` is misspelled as [`fo`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L165)

*/contracts/lending/OndoPriceOracleV2.sol*
* `common` is misspelled as [`comnmon`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L26)

*/contracts/lending/tokens/cErc20ModifiedDelegator.sol*
* `amount` is misspelled as [`amnount`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L154) (1)
* `amount` is misspelled as [`amnount`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L167) (2)
* `setter` is misspelled as [`settor`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L707)

*/contracts/lending/IOndoPriceOracleV2.sol*
* `asset` is misspelled as [`assset`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L95)

### [NC-03] `assert` Used Over `require`

`assert` should only be used in tests. Consider changing all occurrences of `assert` to `require`. Prior to Solidity 0.8 `require` will refund all remaining gas whereas `assert` will not. Even after Solidity 0.8 [`assert` will result in a panic](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require) which should not occur in production code. As stated in the Solidity Documentation: "[p]roperly functioning code should never create a Panic".

*/contracts/cash/factory/CashFactory.sol*
Links: [97](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97).
```solidity
97:	assert(cashProxyAdmin.owner() == guardian);
```

*/contracts/cash/factory/CashKYCSenderFactory.sol*
Links: [106](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106).
```solidity
106:	assert(cashKYCSenderProxyAdmin.owner() == guardian);
```

### [NC-04] Underscore Notation Not Used / Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

*/contracts/lending/JumpRateModelV2.sol*
Links: [29](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29).
```solidity
29:	uint public constant blocksPerYear = 2628000;
```

*/contracts/lending/OndoPriceOracleV2.sol*
Links: [77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77).
```solidity
77:	uint256 public maxChainlinkOracleTimeDelay = 90000;
```

### [NC-05] Named Imports Not Used

All files in scope use file explicit imports in the format: `import './file.sol'`. Consider using name explicit imports for better code clarity: `import {name} from './file.sol'`. 

### [NC-06] Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: type declarations, state variables, events, modifiers, functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol): events are positioned after functions.
* [CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol): events are positioned after functions.
* [CashKYCSenderReceiverFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol): events are positioned after functions.
* [JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol): State is positioned after Events.
* [KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol): events are positioned after functions.
* [OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol): type declarations are positioned after state.
* [CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol): modifiers positioned after functions.
* [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol): modifiers are positioned after functions.
* [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol): modifiers are positioned after functions.
* [IKYCRegistryClient.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/IKYCRegistryClient.sol): events are positioned after functions.
* [IOndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracle.sol): events are positioned after functions.
* [IOndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol): events are positioned after functions.
* [ICashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/ICashManager.sol): events are positioned after functions.

### [NC-07] Explicit Data Types Not Used Consistently

`uint` / `int` default to the explicit data type `uint256` / `int256`. Data types should be explicit like in  [CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol).

*/contracts/lending/JumpRateModelV2.sol*
* All `uint` / `int`'s are not explicit

*/contracts/lending/tokens/cCash/CCash.sol*
* All `uint` / `int`'s are not explicit except for [L37](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L37), and [L159](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L159).

*/contracts/lending/tokens/cToken/CErc20.sol*
* All `uint` / `int`'s are not explicit except for [L37](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L37), and [L159](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L159).

*/contracts/lending/OndoPriceOracleV2.sol*
* [L287](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L287), and [L289](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L289) `uint` / `int`'s are not explicit.

*/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol*
* All `uint` / `int`'s are not explicit except for [L32](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L32), [L137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L137), [L159](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L159), [L160](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L160), [L177](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L177).

*/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol*
* All `uint` / `int`'s are not explicit except for [L30](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L30), [L135](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L135), [L157](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L157), [L158](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L158), [L175](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L175).

*/contracts/lending/tokens/cCash/CTokenCash.sol*
* All `uint` / `int`'s are not explicit except for [L40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L40), [L42](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L42), [L154](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L154), [L169](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L169), [L184](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L184), [L201](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L201), [L210](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L210), [L1356](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1356), [L1367](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1367).

*/contracts/lending/tokens/cToken/CTokenModified.sol*
* All `uint` / `int`'s are not explicit except for [L40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L40), [L42](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L42), [L154](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L154), [L169](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L169), [L184](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L184), [L1359](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1359), [L1370](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1370), [L1371](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1371). 

*/contracts/lending/IOndoPriceOracle.sol*
* `uint` not explicit: [L24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L24).

*/contracts/lending/IOndoPriceOracleV2.sol#L24*
* `uint` not explicit: [L24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L24).

### [NC-08] Contracts Missing `@title` NatSpec Tag

8 out of 30 of the contracts in scope are missing a `@title` tag. Given that 22 contracts all have a `@title` tag, consider adding one per the 8 remaining contracts.

[Proxy.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/Proxy.sol), [Cash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/Cash.sol), [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol), [CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol), [CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol), [IOndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracle.sol), [IOndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol), and [ICashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/ICashManager.sol) are missing a `@title` tag.

### [NC-09] Order of Functions Not Compliant With Solidity Docs

The Solidity Style Guide suggests the following function order: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

* [CCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol): external functions are positioned after public functions.
* [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol): external functions are positioned after public functions.
* [OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol): external functions are positioned after internal functions.
* [CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol): external functions are positioned after private functions.
* [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol): external functions are positioned after public functions.
* [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol): external functions are positioned after internal functions.
* [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol): external functions are positioned after internal functions.

### [NC-10] No License Indication

Some contracts are missing a license indication. If no license is used `SPDX-License-Identifier: UNLICENSED` should be at the top of a contract.

[JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol), and [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol) are missing a license.
