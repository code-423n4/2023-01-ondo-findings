# 1. Splitting `require()` Statements That Use `&&` Saves Gas - (Saves ~`3` Gas per `&&`)

Instead of using the `&&` operator in a single require statement to check multiple conditions, using various require statements with 1 condition per require statement will save ~3 GAS per `&&`.

Affected lines of code:

- [OndoPriceOracleV2.sol#L292-L296](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292-L296)
- [CTokenModified.sol#L45-L48](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L45-L48)

# 2. Save Gas With `payable` Functions

Marking functions as `payable` will be cheaper (by ~20 gas) than using non-`payable` functions because the Solidity compiler inserts a check into non-`payable` functions that requires `msg.value` to be zero.

For instance, the code at [lines 144-L150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144-L150) can be refactored as:

```solidity
  function assignRoletoKYCGroup(
    uint256 kycRequirementGroup,
    bytes32 role
  ) external payable onlyRole(REGISTRY_ADMIN) {
    kycGroupRoles[kycRequirementGroup] = role;
    emit RoleAssignedToKYCGroup(kycRequirementGroup, role);
  }
```

> **Note**: Although this optimization can save gas, it is important to be aware of the security considerations involving Ether held in contracts that it introduces.
> More information on this topic can be found in the [Solidity Compiler Discussion](https://github.com/ethereum/solidity/issues/12539).

Affected line of code:

- [KYCRegistry.sol#L144-L150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144-L150)

# 3. Using `Storage` Instead Of `Memory` For Structs/Arrays Saves Gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array.
If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

As an example, The code on [line 506](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L506) can be refactored as follows:

```solidity
Line 506:    Exp storage exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
```

Affected line of code:

- [CTokenCash.sol#L221](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L221)
- [CTokenCash.sol#L433](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L433)
- [CTokenCash.sol#L506](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L506)
- [CTokenCash.sol#L590](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L590)
- [CTokenCash.sol#L1027](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1027)

# 4. `>`/`<` Is Cheaper Than `>=`/`<=`

Using the `>`/`<` operator requires fewer opcodes and therefore costs less gas than using the `>=`/`<=` operator. This is demonstrated in the tweet linked below:

https://twitter.com/GalloDaSballo/status/1543729467465629696

This optimization can be applied to the following line of code:

- [KYCRegistry.sol#L92](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L92)

# 5. Uncheck Arithmetics Operations That Can’t Underflow/Overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

For example, the code at [KYCRegistry.sol#L163-L165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163-L165)
can be refactored to as:

```solidity
File: main/contracts/cash/kyc/KYCRegistry.sol
   for (uint256 i = 0; i < length;) {
        kycState[kycRequirementGroup][addresses[i]] = false;

        unchecked {
            ++i;
        }
    }
```

Affected line of code:

- [KYCRegistry.sol#L163-L165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163-L165)