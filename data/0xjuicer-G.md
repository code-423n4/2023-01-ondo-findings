## Gas - Creating a old variable isn't necessary.


The following pathern can be seen in multiples places in the code.

```solidity
    uint256 oldPriceCap = fTokenToUnderlyingPriceCap[fToken];
    fTokenToUnderlyingPriceCap[fToken] = value;
    emit PriceCapSet(fToken, oldPriceCap, value);
```

It can be replaced by:

```solidity
    emit PriceCapSet(fToken, fTokenToUnderlyingPriceCap[fToken], value);
    fTokenToUnderlyingPriceCap[fToken] = value;
```

The following pathern can be found on the following files:
- OndoPriceOracleV2.sol
- CashManager.sol
- OndoPriceOracle.sol
- KYCRegistryClient.sol
- And some more from compound

## Gas - Use merkle trees instead of array of addresses

On the `completeRedemptions()` function, the input has two arrays, `redeemers` and `refundees`, I suggest to use two merkle tree instead of two lists of addresses in order to save some gas when submiting the `completeRedemptions`.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L708-L709