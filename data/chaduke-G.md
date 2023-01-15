G1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L123
Putting it inside unchecked can save gas since overflow is impossible here:
```
uint dstTokensNew = accountTokens[dst] + tokens;

```