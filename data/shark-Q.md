## 0. Zero price is considered valid

In the following contract, `int answer` is required to be `>= 0`. However, this allows `int answer` to be zero, meaning a pointlessly scaled zero value could be returned.

File: `OndoPriceOracleV2.sol` [Line 297](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L297)

```solidity
    require(answer >= 0, "Price cannot be negative");
```

Instead of the above, consider refactoring to:

```solidity
    require(answer > 0, "Price must be greater than zero");
```

## 1. Use time units when applicable

See this: [docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html#time-units](https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html#time-units)

With that in mind, the following declaration may be refactored:

File: `OndoPriceOracleV2.sol` [Line 77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)

```solidity
  uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
```

From the above to, this:

```solidity
  uint256 public maxChainlinkOracleTimeDelay = 25 hours;
```

## 2. Use delete to clear variables instead of zero assignment

Using the [`delete`](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) keyword more clearly states your intention.

For example:

File: `CashManager.sol` [Line 259](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L259)

```solidity
    mintRequestsPerEpoch[epochToClaim][user] = 0;
```

The above instance could be refactored to use the `delete` keyword:

```solidity
    delete mintRequestsPerEpoch[epochToClaim][user];
```

## 3. Typo mistakes

It is not recommended to spell words incorrectly as this will decreases readability. However, this is issue is present in many contracts. Consider fixing the following typos to increase readability:

File: `KYCRegistry.sol` ([Line 62](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L62), [Line 205](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L205), [Line 236](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L236))

```
      /// @audit sucessfully -> successfully       vvvvvvvvvvv
62:   *         `kycRequirementGroup`. In order to sucessfully call this function,

      /// @audit elligible -> eligible                       vvvvvvvvv
205:  * @param addresses   Array of addresses being added as elligible

      /// @audit same typo above..
236:  * @param addresses   Array of addresses being added as elligible
```

File: `CCash.sol` [Line 117](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L117), [Line 165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L165)
File: `CErc20.sol` [Line 117](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L117), [Line 165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L165)

```
       /// @audit payed -> paid                          vvvvv
117:   * @param borrower the account with the debt being payed off

       /// @audit fo --> of          vv
165:   * @param addAmount The amount fo underlying token to add as reserves
```

File: `OndoPriceOracleV2.sol` [Line 26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L26)

```
    /// @audit comnmon -> common                   vvvvvvv
26: /// @notice Helper interface for standardizing comnmon calls to
```

File: `CashManager.sol` ([Line 121](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L121), [Line 333](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L333))

```
      /// @audit "Role Based" -> "Role-Based"
121:  /// @dev Role Based Access control members

       /// @audit "front running" --> "front-running"
333:   * @dev `oldBalance` is provided to prevent front running attacks where a
```

## 4. Use specific imports instead of global imports

Using import declarations will speed up compilation, and avoids polluting the namespace making flattened files smaller.

Here are some instances of this issue:

- File: `Cash.sol` [Line 18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L18)
- File: `CashKYCSender.sol` [Line 18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L18)
- File: `CashKYCSender.sol` [Line 19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L19)
- File: `CashKYCSenderReceiver.sol` [Line 18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L18)
- File: `CashKYCSenderReceiver.sol` [Line 19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L19)

## 5. Interfaces are not prefixed with `I`

Interfaces names should be prefixed with `I`. However, some interfaces are not following this rule. This can lead to confusion due to inconsistency with some being prefixed and some not.

- File: `CCash.sol` [Line 6](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L6)
- File: `cErc20ModifiedDelegator.sol` [Line 9](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L9)
- File: `IOndoPriceOracle.sol` [Line 18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L18)
- File: `OndoPriceOracle.sol` [Line 21](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L21)
- File: `OndoPriceOracle.sol` [Line 27](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L27)

## 6. Use of `uint` instead of `uint256`

`uint` is just a shorthand of `uint256`. As such, consider replacing any instances of `uint` with `uint256`. This will improve explicitness.

- File: [`JumpRateModelV2.sol`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol) (entire contract)
- File: `IOndoPriceOracle.sol` [Line 24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L24)
- File: `IOndoPriceOracleV2.sol` [Line 24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L24)
