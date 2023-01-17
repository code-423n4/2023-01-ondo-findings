# low

## L-1 incorrect comment or check

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L408

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L413

```javascript
File: cash/CashManager.sol

408:    * @dev The maximum fee that can be set is 10_000 bps, or 100%
```

If you look at the check it does `_mintFee >= BPS_DENOMINATOR`:

```javascript
File: cash/CashManager.sol

413:    if (_mintFee >= BPS_DENOMINATOR) {
414:      revert MintFeeTooLarge();
415:    }
```

So the maximum `mintFee` is `9999`, 99.99%