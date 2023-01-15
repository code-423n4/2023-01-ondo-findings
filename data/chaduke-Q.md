GA1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L576-L587
We need to check that only one of ``redeemTokensIn`` and ``redeemAmountIn`` is greater than zero, and the other is equal to zero.
```
require(
      (redeemTokensIn >0 && redeemAmountIn == 0) || (redeemTokensIn == 0 && redeemAmountIn > 0),
      "Only one of redeemTokensIn and redeemAmountIn must be zero"
    );
```

