## Uncheckable math

epochDuration != 0 and (block.timestamp % epochDuration) always less than block.timestamp

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L583-L585

```solidity
      currentEpochStartTimestamp =
        block.timestamp -
        (block.timestamp % epochDuration);
```

## No need to cache calldata length

It is cheaper to read length from calldata array than caching it in memory

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L749-L750

```solidity
    uint256 size = redeemers.length;
    for (uint256 i = 0; i < size; ++i) {
```