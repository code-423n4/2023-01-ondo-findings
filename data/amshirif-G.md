Require statements are used after making calls/not the first line(s) of a function call. In most cases only a very small amount of gas would be lost. I will list them below, however they do not amount to much. The main concern would be in contracts `contracts/lending/tokens/cCash/CCashDelegate.sol` and `contracts/lending/tokens/cToken/CTokenDelegate.sol`. The reason being is the stated intention (in the code comments) that an update using the `data` field is to come in the future. It should be noted that all logic is done after initial checks. In both cases here is a recommended version.

Both data and the unnecessary check to set to zero can be removed without any alteration to the code's behavior. (This in itself could be considered an optimization). 

```solidity
function _becomeImplementation(bytes) public virtual override {
    require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"
    );
  }
```

Here are all the occurrences, most of these calls are minor unless `_beforeTokenTransfer()` is changed. 

https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cCash/CCashDelegate.sol#L21-L34

https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cToken/CTokenDelegate.sol#L21-L34

https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/token/Cash.sol#L29-L40

https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/token/CashKYCSender.sol#L56-L75

https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/token/CashKYCSenderReceiver.sol#L56-L83