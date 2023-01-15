# Yondo Finance Gas optimization report

## [G-01] Avoiding the use of compound assignment operators.

Using compound assignment operators like `x+=i` instead of `x=x+1` is more expensive in terms of gas than the normal syntax. 

Instances: 

[CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/CashManager.sol#L582)