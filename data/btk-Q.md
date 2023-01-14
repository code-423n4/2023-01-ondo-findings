### Total L issues

| Number | Issues Details                                                                     | Context |
|--------|------------------------------------------------------------------------------------|---------|
| [L-01] | Avoid shadowing inherited state variables                                          |   4     |
| [L-02] | `initialize()` function can be called by anybody                                   |   3     |
| [L-03] | No Storage Gap for Upgradeable contracts                                           |   6     |
| [L-04] | Low level calls with solidity version 0.8.14 and lower can result in optimiser bug |   3     |
| [L-05] | Loss of precision due to rounding                                                  |   1     |

### Total NC issues

| Number  | Issues Details                                                          | Context       |
|---------|-------------------------------------------------------------------------|---------------|
| [NC-01] | Use `require()` instead of `assert()`                                   | 3             |
| [NC-02] | Critical changes should use-two step procedure                          | 3             |
| [NC-03] | Include `@return` parameters in NatSpec comments                        | 14            |
| [NC-04] | Function writing does not comply with the `Solidity Style Guide`   | All Contracts |
| [NC-05] | Solidity compiler optimizations can be problematic                      | 1             |
| [NC-06] | Non-usage of specific imports                                           | All Contracts |
| [NC-07] | Mark visibility of `initialize()` functions as external                 | 6             |
| [NC-08] | Add address(0) check for the critical changes                           | 3             |

## [L-01] Avoid shadowing inherited state variables

In `CashKYCSenderReceiver.sol` there is a local variables named `name` `symbol` `kycRegistry` `kycRequirementGroup`, but there is functions and state variables in the inherited contracts ( `ERC20Upgradeable.sol` `KYCRegistryClient.sol` ) with the same name. This use causes compilers to issue warnings, negatively affecting checking and code readability.

```solidity
    __ERC20PresetMinterPauser_init(name, symbol);
    __KYCRegistryClientInitializable_init(kycRegistry, kycRequirementGroup);
```

### Lines of code

- [CashKYCSenderReceiver.sol:52](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L52)
- [CashKYCSenderReceiver.sol:53](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L53)

### Recommended Mitigation Steps
Avoid using variables with the same name.

## [L-02] `initialize()` function can be called by anybody

`initialize()` function can be called anybody when the contract is not initialized.

```solidity
  function initialize(
    string memory name,
    string memory symbol,
    address kycRegistry,
    uint256 kycRequirementGroup
  ) public initializer {
    __ERC20PresetMinterPauser_init(name, symbol);
    __KYCRegistryClientInitializable_init(kycRegistry, kycRequirementGroup);
  }
```

### Lines of code

- [CashKYCSender.sol:46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L46)
- [CashKYCSenderReceiver.sol:46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L46)
- [CCash.sol:30](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L30)

### Recommended Mitigation Steps

Add a `DEPLOYER` address and require that only him can call the `initialize()` function.

## [L-03] No Storage Gap for Upgradeable contracts

For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

### Lines of code

- [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
- [CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)
- [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)
- [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol)
- [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol)
- [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol)

### Recommended Mitigation Steps

Consider adding a storage gap at the end of the upgradeable contract:
```solidity
  /**
   * @dev This empty reserved space is put in place to allow future versions to add new
   * variables without shifting down storage in the inheritance chain.
   * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
   */
  uint256[50] private __gap;
```

## [L-04] Low level calls with solidity version 0.8.14 and lower can result in optimiser bug

The protocol is using low level calls with solidity version less then 0.8.14 which can result in optimizer bug.

Unlike many of our prior bug disclosures, this bug does not rely on invalid or maliciously crafted inputs; rather, under certain compiler/optimizer settings the Solidity compiler will delete memory operations it (mistakenly) considers as having no effect on final contract output.

In particular, using the new YUL optimizer and legacy code generation (i.e., with compiler flags --optimize and without --via-ir) memory operations (i.e., returndatacopy, mstore, etc.) can be removed from the output of the compiler even when they can affect the execution output. The cause of this bug is where and how the YUL optimizer is run. If an inline assembly block does not mention any Solidity variables, it is assumed to be self-contained. In this case, the YUL optimizer is applied to the inline assembly if it executes in total isolation, i.e., as if execution immediately halted after the last command. Under the above (faulty) assumption, it would be safe to remove commands like mstore if they were provably not read within the block. However, there can be arbitrary code execution after the optimized assembly block completes, some of which could observe the effects of these memory mutations, meaning it is not safe to remove.

> Ref: https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

### Lines of code 

- [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol)
- [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol)
- [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)

### Recommended Mitigation Steps

Consider upgrading to solidity 0.8.17

## [L-05] Loss of precision due to rounding   

Loss of precision due to rounding to in `_getMintFees` function.

```solidity
    return (collateralAmount * mintFee) / BPS_DENOMINATOR;
```

### Lines of code

- [CashManager.sol:505](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L505)

### Recommended Mitigation Steps
Add scalars so roundings are negligible

## [NC-01] Use `require()` instead of `assert()`

Assert should not be used except for tests, require should be used. Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as `request()/revert()` did.

The big difference between the two is that the `assert()` function when false, uses up all the remaining gas and reverts all the changes made. Meanwhile, a `require()` statement when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type `Panic(uint256)`. Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### Lines of code

- [CashKYCSenderReceiverFactory.sol:10](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106)
- [CashKYCSenderFactory.sol:106](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106)
- [CashFactory.sol:97](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97)

### Recommended Mitigation Steps

Use `require()` instead of `assert()`

## [NC-02] Critical changes should use-two step procedure

Lack of two-step procedure for critical operations leaves them error-prone.

### Lines of code

- [CashManager.sol:452](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L452)
- [CashManager.sol:465](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L465)
- [CashManager.sol:803](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L803)

### Recommended Mitigation Steps

Consider adding two step procedure on the critical functions.

## [NC-03] Include return parameters in NatSpec comments

If Return parameters are declared, you must prefix them with `/// @return`. Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

### Lines of code

- [OndoPriceOracle.sol:63](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L63)
- [CashFactory.sol:125](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L125)
- [CashKYCSenderFactory.sol:135](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L135)
- [CashKYCSenderReceiverFactory.sol:135](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L135)
- [KYCRegistry.sol:115](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L115)
- [CCash.sol:196](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L196)
- [cToken/CErc20.sol:196](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L196)
- [OndoPriceOracleV2.sol:94](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L94)
- [OndoPriceOracleV2.sol:279](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L279)
- [CashManager.sol:504](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L504)
- [CashManager.sol:514](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L514)
- [CashManager.sol:836](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L836)
- [CashManager.sol:958](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L958)
- [cErc20ModifiedDelegator.sol:1059](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1059)

### Recommended Mitigation Steps

Include the `@return` argument in the NatSpec comments.

## [NC-04] Function writing does not comply with the `Solidity Style Guide`

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external / public / internal / private`
- `view / pure`

### Recommended Mitigation Steps

Follow Solidity Style Guide.

## [NC-05] Solidity compiler optimizations can be problematic

```solidity
const config: HardhatUserConfig = {
  solidity: {
    compilers: [
      {
        version: "0.8.16",
        settings: {
          optimizer: {
            enabled: true,
            runs: 100,
          },
        },
      }
```

Description: Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Lines of code

- [hardhat.config.ts:25](https://github.com/code-423n4/2023-01-ondo/blob/main/hardhat.config.ts#L25-L36)

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug. Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [NC-06] Non-usage of specific imports 

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly.

- https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

### Recommended Mitigation Steps
Use specific imports syntax per [solidity](https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files) docs recommendation.

## [NC-07] Mark visibility of `initialize()` functions as external  

- If someone wants to extend via inheritance, it might make more sense that the overridden `initialize()` function calls the internal {...}_init function, not the parent public `initialize()` function.

- External instead of public would give more the sense of the `initialize()` functions to behave like a constructor (only called on deployment, so should only be called externally)

- Security point of view, it might be safer so that it cannot be called internally by accident in the child contract

- It might cost a bit less gas to use external over public

- It is possible to override a function from external to public ("opening it up") but it is not possible to override a function from public to external ("narrow it down").

> Ref: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750

### Lines of code

- [CTokenModified.sol:43](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L43)
- [CErc20.sol:40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L40)
- [CTokenCash.sol:43](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L43)
- [CCash.sol:40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L40)
- [CashKYCSenderReceiver.sol:51](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L51)
- [CashKYCSender.sol:51](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L51)

### Recommended Mitigation Steps

Change the visibility of `initialize()` functions to external

## [NC-08] Add address(0) check for the critical changes

Check of the address for the critical changes to protect the code from address(0) problem in case of mistakes.

### Lines of code

- [CashManager.sol:452](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L452)
- [CashManager.sol:465](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L465)
- [CashManager.sol:803](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L803)

### Recommended Mitigation Steps

```solidity
    if (_feeRecipient == address (0)) revert();
```

