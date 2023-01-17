- 0 address check
0 address control should be done in these parts;

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L54

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L54

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54

---
open pragma
Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L1

https://swcregistry.io/docs/SWC-103

https://github.com/Uniswap/v3-core/blob/main/contracts/UniswapV3Pool.sol#L2




