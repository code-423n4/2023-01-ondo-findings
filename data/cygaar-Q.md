#1: OpenZeppelin [recommends](https://docs.openzeppelin.com/contracts/4.x/api/proxy#transparent-vs-uups) using UUPSUpgradeable proxies instead of TransparentUpgradeable proxies: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L20.

#2: `setPendingMintBalance` doesn't update `currentMintAmount` which makes it inconsistent with the behavior in `_checkAndUpdateMintLimit`: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L336.

#3: Similarly, `setPendingRedemptionBalance` does not update `currentRedeemAmount`:  https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L851.

Listed #2 and #3 are low in case this is expected behavior, otherwise I would've bumped these up to medium.

#4: `minimumRedeemAmount` should prevent setting the limit to 0: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L817. Having it be 0 means you can call `requestRedemption` successfully albeit with a no-op.

#5: `redeemLimit` in CashManager.sol isn't explicitly related to `mintLimit`. It seems that this value can be set arbitrarily high or low which doesn't make sense because it should be a function of collateral deposited and the exchange rate. While this isn't a vulnerability per se, it does mean the contract owner can set arbitrary rules.

#6: This [function](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L626) will revert with an underflow error instead of `MintExceedsRateLimit` in certain scenarios. Let's say `mintLimit` is 100, `currentMintAmount` is 99, and `collateralAmountIn` is 2. The function would update `currentMintAmount` to 101 after. On the next call, `mintLimit - currentMintAmount` will underflow leading to an underflow error before the `MintExceedsRateLimit` revert. You can instead write this line as `if (collateralAmountIn + currentMintAmount > mintLimit)` to achieve your goal.

#7: Very similar to the above, but for `_checkAndUpdateRedeemLimit`. This [line](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L645) can be rewritten to avoid an underflow issue.

#8 If the contract manager [shrinks](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L546) the `epochDuration` value, this may end up with an epoch value being skipped. For example, let's say the current `epochDuration` is 100 minutes and we're currently in epoch 1, and we're currently 51 minutes into it. If the duration is set to 25 and `transitionEpoch` is called, our new `currentEpoch` will be 3, which means we skipped over epoch 2. It doesn't look like there are too many downstream effects of this happening, but it does lead to some inconsistency.