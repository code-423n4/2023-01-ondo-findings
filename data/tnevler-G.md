# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i = 0; i < exCallData.length; ++i) {``` [L127](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127) 
1. ```for (uint256 i = 0; i < exCallData.length; ++i) {``` [L137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137) 
1. ```for (uint256 i = 0; i < exCallData.length; ++i) {``` [L137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137) 
1. ```for (uint256 i = 0; i < length; i++) {``` [L163](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163) 
1. ```for (uint256 i = 0; i < length; i++) {``` [L180](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180) 
1. ```for (uint256 i = 0; i < size; ++i) {``` [L750](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750) 
1. ```for (uint256 i = 0; i < size; ++i) {``` [L786](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786) 
1. ```for (uint256 i = 0; i < size; ++i) {``` [L933](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933) 
1. ```for (uint256 i = 0; i < exCallData.length; ++i) {``` [L961](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```rateDifference = exchangeRate - lastSetMintExchangeRate;``` [L297](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L297) 
1. ```rateDifference = lastSetMintExchangeRate - exchangeRate;``` [L299](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L299) 
1. ```redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;``` [L865](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L865) (previousBalance - balance)
1. ```redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;``` [L867](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L867) 
1. ```totalReservesNew = totalReserves - reduceAmount;``` [L1281](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1281) 
1. ```totalReservesNew = totalReserves - reduceAmount;``` [L1284](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1284) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
