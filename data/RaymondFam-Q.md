## Missing minter role granting
In `deployCash()` of CashFactory.sol, `cashProxied.grantRole()` did not grant `MINTER_ROLE` to `guardian` address. Although this can later be separately excuted by the guardian who is now the default admin, consider having this specific role granting included in this atomic call or having it commented in the NatSpec the reason for this omission.

[File: CashFactory.sol#L75-L110](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L75-L110)

```diff
    cashProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashProxied.grantRole(PAUSER_ROLE, guardian);
+    cashProxied.grantRole(MINTER_ROLE, guardian);
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

Here are the instances entailed:

[File: CashFactory.sol#L128](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L128)

```solidity
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
```