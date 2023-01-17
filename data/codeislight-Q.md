1. unecessary check for zero amount in CashManager._checkAndUpdateRedeemLimit(uint256)
```
        if (amount == 0) {
            revert RedeemAmountCannotBeZero();
        }
```
in this case there is a check for zero amount which is followed by:
```
      if (amount > redeemLimit - currentRedeemAmount) {
            revert RedeemExceedsRateLimit();
      }
```
we can notice that the least value possible for the right side of ">" operator is 0, which always would ensure that `amount` is greater than zero. therefore, we can safely remove the revert check for zero amount.