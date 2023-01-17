### Need to check for address(0), before setting the `guardian` on `CashFactory`

The ´constructor` of the contract `CashFactory` should check if `_guardian` against `address(0)` before setting the `guardian`. There has been many  severe impacts in the past of DeFi that proves we should check constrains even when the deployer is expected to be secure/reliable.

[CashFactory.sol#L54](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L54`)