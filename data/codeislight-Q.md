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
2. check for address code size to be greater than zero, when a function has a contract address parameter.
3. using unchecked in for loops ++i
in CashManager:
772:        for (uint256 i = 0; i < size; ++i) {
814:        for (uint256 i = 0; i < size; ++i) {
973:        for (uint256 i = 0; i < size; ++i) {
999:        for (uint256 i = 0; i < exCallData.length; ++i) {
in CashFactory:
127:        for (uint256 i = 0; i < exCallData.length; ++i) {
in CashKYCSenderReceiverFactory:
137:        for (uint256 i = 0; i < exCallData.length; ++i) {
in CashKYCSenderFactory:
137:        for (uint256 i = 0; i < exCallData.length; ++i) {
in KYCRegistry:
163:        for (uint256 i = 0; i < length; i++) {
180:        for (uint256 i = 0; i < length; i++) {
and in other instances ...