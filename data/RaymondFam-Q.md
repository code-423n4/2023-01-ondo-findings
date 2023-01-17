## Missing minter role granting
In `deployCash()` of CashFactory.sol, `cashProxied.grantRole()` did not grant `MINTER_ROLE` to `guardian` address. Similarly, in `deployCashKYCSender()` of CashKYCSenderFactory.sol, `cashKYCSenderProxied.` did not grant `MINTER_ROLE` to `guardian` address either. (Note: The same instance also goes with `deployCashKYCSenderReceiver()` of CashKYCSenderReceiverFactory.sol). Although this can later be separately executed by the guardian who is now the default admin, consider having this specific role granting included in the atomic function call or having it commented in the NatSpec the reason for this omission.

[File: CashFactory.sol#L75-L110](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L75-L110)

```diff
    cashProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashProxied.grantRole(PAUSER_ROLE, guardian);
+    cashProxied.grantRole(MINTER_ROLE, guardian);
```

[File: CashKYCSenderFactory.sol#L98-L99](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L98-L99)

```diff
    cashKYCSenderProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashKYCSenderProxied.grantRole(PAUSER_ROLE, guardian);
+    cashKYCSenderProxied.grantRole(MINTER_ROLE, guardian);
```
[File: CashKYCSenderReceiverFactory.sol#L98-L99](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L98-L99)

```diff
    cashKYCSenderReceiverProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashKYCSenderReceiverProxied.grantRole(PAUSER_ROLE, guardian);
+    cashKYCSenderReceiverProxied.grantRole(MINTER_ROLE, guardian);
```
## Use of deprecated file
As denoted in [OpenZeppelin's ERC20PresetMinterPauser.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/presets/ERC20PresetMinterPauser.sol#L26) as well as [Ondo Finance's ERC20PresetMinterPauserUpgradeable.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol#L27):

This particular file is now deprecated in favor of [https://wizard.openzeppelin.com/[Contracts Wizard]](https://wizard.openzeppelin.com/). 

Specifically, `cashProxied.initialize(name, ticker)` is invoked in `deployCash()` of CashFactory.sol. This leads to calling the parental `initialize()` in ERC20PresetMinterPauserUpgradeable.sol, which is discouraged.

[File: CashFactory.sol#L87](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L87)

```solidity
    cashProxied.initialize(name, ticker);
```
[File: ERC20PresetMinterPauserUpgradeable.sol#L36-L42](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol#L36-L42)

```solidity
  function initialize(string memory name, string memory symbol)
    public
    virtual
    initializer
  {
    __ERC20PresetMinterPauser_init(name, symbol);
  }
```
Likewise, cashKYCSenderProxied.initialize(name, ticker, registry, kycRequirementGroup) is invoked in `deployCashKYCSender()` of CashKYCSenderFactory.sol, leading to the parental call on `__ERC20PresetMinterPauser_init(name, symbol)` in ERC20PresetMinterPauserUpgradeable.sol. (Note: An identical instance also happens on `deployCashKYCSenderReceiver()` of CashKYCSenderReceiverFactory.sol.)

[File: CashKYCSenderFactory.sol#L91-L96](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L91-L96)

```solidity
    cashKYCSenderProxied.initialize(
      name,
      ticker,
      registry,
      kycRequirementGroup
    );
```
[File: CashKYCSender.sol#L46-L54](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L46-L54)

```solidity
    __ERC20PresetMinterPauser_init(name, symbol);
```
[File: ERC20PresetMinterPauserUpgradeable.sol#L53-L60](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol#L53-L60)

```solidity
  function __ERC20PresetMinterPauser_init(
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are the contract instances lacking partial/full NatSpec that will be of added values to the users and developers if adequately provided:

[File: Proxy.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol)
[File: Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)
[File: CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
[File: CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

## No Storage Gap for Upgradeable Contracts
Consider adding a storage gap at the end of an upgradeable contract, just in case it would entail some child contracts in the future. This would ensure no shifting down of storage in the inheritance chain.

```diff
+ uint256[50] private __gap;
```
Here are the contract instances entailed:

[File: Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)
[File: CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
[File: CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

## Interfaces should be imported
Some contracts have interface(s) showing up in its/their entirety at the top/bottom of the contract facilitating an ease of references on the same file page. This has, in certain instances, made the already large contract size to house over a thousand code lines. Consider saving the interfaces entailed as `Ixxx.sol` respectively, and have them imported like all other files.

Here are some of the contract instances entailed:

[File: cErc20ModifiedDelegator.sol#L9-L134](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L9-L134)
[File: OndoPriceOracle.sol#L21-L29](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L21-L29)
[File: IOndoPriceOracle.sol#L18-L25](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L18-L25)
[File: CCash.sol#L6-L8](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L6-L8)
[File: OndoPriceOracleV2.sol#L22-L36](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L22-L36)
[File: CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol)
[File: cErc20ModifiedDelegator.sol#L9-L134](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L9-L134)
[File: cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol)

## Unspecific compiler version pragma
For some source-units the compiler version pragma is very unspecific, i.e. ^0.5.12, ^0.8.10 etc. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: CashKYCSender.sol#L18-L19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L18-L19)

```diff
- import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
- import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

+ import {ERC20PresetMinterPauserUpgradeable} from "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
+ import {KYCRegistryClientInitializable} from "contracts/cash/kyc/KYCRegistryClientInitializable.sol";
```
## Inconsistency in interface naming
Some interfaces in the code bases are named without the prefix `I` that could cause confusion to developers and readers referencing or interacting with the protocol. Consider conforming to Solidity's naming conventions by having the instances below refactored as follow:

[File: cErc20ModifiedDelegator.sol#L9](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L9)

```diff
- interface ComptrollerInterface {
+ interface IComptrollerInterface {
```
[File: OndoPriceOracle.sol#L21](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L21)

```diff
- interface CTokenOracle {
+ interface ICTokenOracle {
```
[File: OndoPriceOracle.sol#L27](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L27)

```diff
- interface CTokenLike {
+ interface ICTokenLike {
```
[File: IOndoPriceOracle.sol#L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L18)

```diff
- interface PriceOracle {
+ interface IPriceOracle {
```
[File: CCash.sol#L6](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L6)

```diff
- interface CompLike {
+ interface ICompLike {
```
All other instances entailed:

[File: OndoPriceOracleV2.sol#L22-L30](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L22-L30)
[File: cErc20ModifiedDelegator.sol#L9](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L9)
[File: cErc20ModifiedDelegator.sol#L144](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L144)

## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed.

## Missing checks for contract existence
Performing low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed as documented in the link below:

https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions

Consider having account existence checked prior to making `call` where possible. 

Here are the three instances unanimously using the same/identical function logic:

[File: CashFactory.sol#L128-L133](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L128-L133)
[File: CashKYCSenderFactory.sol#L137-L142](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137-L142)
[File: CashKYCSenderReceiverFactory.sol#L137-L143](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137-L143)

```solidity
    for (uint256 i = 0; i < exCallData.length; ++i) {
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
      require(success, "Call Failed");
      results[i] = ret;
```
## `uint256` over `uint`
Across the codebase, there are numerous instances of uint, as opposed to uint256. In favor of explicitness, consider replacing all instances of uint with uint256.

Here are some of the contract instances entailed:

[File: JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol)
[File: CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)

## Imprecise block time
Since resorting to POS, the Ethereum average block time is hardly 12 seconds according to [the daily ycharts](https://ycharts.com/indicators/ethereum_average_block_time#:~:text=Basic%20Info,8.72%25%20from%20one%20year%20ago.). 12 seconds per block only happens when no validator(s) is/are missing any slot(s) or turn(s) idling. For this reason, `baseRatePerBlock`, `multiplierPerBlock`, and `jumpMultiplierPerBlock` tend to be always slightly lesser than their actual values. Consider switching to precise time units in seconds if possible.

[File: JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol)

```solidity
29:  uint public constant blocksPerYear = 2628000; // block.time == 12

177:    baseRatePerBlock = baseRatePerYear.div(blocksPerYear);
178:    multiplierPerBlock = (multiplierPerYear.mul(1e18)).div(
179:      blocksPerYear.mul(kink_)
180:    );
181:    jumpMultiplierPerBlock = jumpMultiplierPerYear.div(blocksPerYear);
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a = false` instance below may be refactored as follows:

[File: KYCRegistry.sol#L181](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L181)

```diff
-      kycState[kycRequirementGroup][addresses[i]] = false;
+      delete  kycState[kycRequirementGroup][addresses[i]];
```
## More robust sanity check
A sanity check was performed on `underlying_` in `initialize()` of CCash.sol and CErc20.sol. Consider refactoring the code line entailed below just in case `totalSupply()` would not revert on failure:

[File: CCash.sol#L53-L55](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L53-L55)   
[File: CErc20.sol#L53-L55](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L53-L55)

```diff
    // Set underlying and sanity check it
    underlying = underlying_;
-    EIP20Interface(underlying).totalSupply();
+    require(EIP20Interface(underlying).totalSupply() != 0);
```
## Time Units
According to:

https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html

suffixes like `seconds`, `minutes`, `hours`, `days` and `weeks` after literal numbers can be used to specify units of time where seconds are the base unit and units are considered naively in the following way:

1 == 1 seconds
1 minutes == 60 seconds
1 hours == 60 minutes
1 days == 24 hours
1 weeks == 7 days

To avoid human error while making the assignment more verbose, the following variable may be rewritten as:

[File: OndoPriceOracleV2.sol#L77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)

```diff
-  uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
+  uint256 public maxChainlinkOracleTimeDelay = 25 hours;
```
## Valid price could include zero value
in `getChainlinkOraclePrice()` of OndoPriceOracleV2.sol, `answer == 0`, i.e. a meaningless zero scaled price could be returned. Consider having the affected code refactored as follows:

[File: OndoPriceOracleV2.sol#L297](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L297)

```diff
-    require(answer >= 0, "Price cannot be negative");
+    require(answer > 0, "Price cannot be negative or zero");
```
Better yet, set a low boundary to the lowest price acceptable to handle edge cases entailing dusts in very low wei.