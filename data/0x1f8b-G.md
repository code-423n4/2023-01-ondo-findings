- [Gas](#gas)
    - [**1. Optimize sanction List**](#1-optimize-sanction-list)
    - [**2. Remove return**](#2-remove-return)
    - [**3. Use a recent solidity version**](#3-use-a-recent-solidity-version)
    - [**4. Use require instead of assert**](#4-use-require-instead-of-assert)
    - [**5. Avoid compound assignment operator in state variables**](#5-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 8 = 104**](#total-gas-saved-13--8--104)
    - [**6. delete optimization**](#6-delete-optimization)
        - [Total gas saved: **5 * 4 = 20**](#total-gas-saved-5--4--20)
    - [**7. Remove natspec complaints**](#7-remove-natspec-complaints)
    - [**8. Optimize CErc20.initialize and CCash.initialize**](#8-optimize-cerc20initialize-and-ccashinitialize)
    - [**9. Remove SafeMath from JumpRateModelV2**](#9-remove-safemath-from-jumpratemodelv2)

# Gas

## **1. Optimize sanction List**

If the `ISanctionsList` interface had an `isSanctionedAny` method, you could check multiple addresses at once without jumping into the contract multiple times.

```js
    require(!sanctionsList.isSanctioned(spender), "Spender is sanctioned");
    require(!sanctionsList.isSanctioned(src), "Source is sanctioned");
    require(!sanctionsList.isSanctioned(dst), "Destination is sanctioned");
```

**Affected source code:**

- [CTokenModified.sol:97-99](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L97-L99)

## **2. Remove return**

It always returns `NO_ERROR`, it is better not to return or check the value.

**Affected source code:**

- [CTokenModified.sol:143](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L143)
- [CTokenModified.sol:401](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L401)
- [CTokenModified.sol:471](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L471)
- [CTokenCash.sol:401](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L401)
- [CTokenCash.sol:471](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L471)
- [CTokenCash.sol:1077](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1077)
- [CTokenCash.sol:1104](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1104)
- [CTokenCash.sol:1130](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1130)
- [CTokenCash.sol:1291](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1291)
- [CTokenCash.sol:1345](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1345)
- [CErc20.sol:68](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L68)
- [CErc20.sol:79](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L79)
- [CErc20.sol:92](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L92)
- [CErc20.sol:102](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L102)
- [CErc20.sol:112](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L112)
- [CErc20.sol:126](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L126)
- [CErc20.sol:143](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L143)
- [CCash.sol:68](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L68)
- [CCash.sol:79](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L79)
- [CCash.sol:92](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L92)
- [CCash.sol:102](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L102)
- [CCash.sol:112](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L112)
- [CCash.sol:126](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L126)
- [CCash.sol:143](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L143)

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/

## **3. Use a recent solidity version**

Using an updated version of solidity you can achieve significant gas savings, like the one mentioned above:

- With at least 0.8.10 to skip existence check for external contract if return data is expected. In this case, the ABI decoder will revert if the contract does not exist.

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

**Affected source code:**

- [ERC721Upgradeable.sol:479](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol#L479)

## **4. Use `require` instead of `assert`**

The `assert()` and `require()` functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the `assert()` function when false, **uses up all the remaining gas and reverts all the changes made.**

Meanwhile, a `require()` function when false, also reverts back all the changes made to the contract but **does refund all the remaining gas fees we offered to pay**. This is the most common Solidity function used by developers for debugging and error handling.

**Affected source code:**

- [CashFactory.sol:97](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L97)
- [CashKYCSenderFactory.sol:106](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L106)
- [CashKYCSenderReceiverFactory.sol:106](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106)


## **5. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint256 private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
Uint256 private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [CashManager.sol:221](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L221)
- [CashManager.sol:582](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L582)
- [CashManager.sol:630](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L630)
- [CashManager.sol:649](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L649)
- [CashManager.sol:678-680](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L678-L680)
- [CashManager.sol:681](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L681)
- [CashManager.sol:865](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L865)
- [CashManager.sol:867](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L867)

### Total gas saved: **13 * 8 = 104**

## **6. `delete` optimization**

Use `delete` instead of set to default value (`false` or `0`).

5 gas could be saved per entry in the following affected lines:

**Affected source code:**

- [CashManager.sol:259](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L259)
- [CashManager.sol:754](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L754)
- [CashManager.sol:790](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L790)
- [KYCRegistry.sol:181](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L181)

### Total gas saved: **5 * 4 = 20**

## **7. Remove natspec complaints**

This is not the best way to avoid some warnings and it will increase the contract sizes. Remove natspec complaints in order to save gas. 

**Affected source code:**

- [CCashDelegate.sol:23-28](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L23-L28)
- [CCashDelegate.sol:41-43](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L41-L43)
- [CTokenDelegate.sol:23-28](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenDelegate.sol#L23-L28)
- [CTokenDelegate.sol:41-43](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenDelegate.sol#L41-L43)

## **8. Optimize `CErc20.initialize` and `CCash.initialize`**

It's better to use the local variable instead of the storage one.

```diff
function initialize(
    address underlying_,
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address kycRegistry_,
    uint kycRequirementGroup_
  ) public {
    ...

    // Set underlying and sanity check it
+   EIP20Interface(underlying_).totalSupply();
    underlying = underlying_;
-   EIP20Interface(underlying).totalSupply();
  }
```

**Affected source code:**

- [CErc20.sol:54-55](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L54-L55)
- [CCash.sol:54-55](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L54-L55)

## **9. Remove `SafeMath` from `JumpRateModelV2`**

The code use SafeMath, but it will be compiled in 0.8.X version of solidity, so the overflows checks will be added, and also will be checked by code, wasting resources and gas.

**Affected source code:**

- [JumpRateModelV2.sol:4](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L4)

