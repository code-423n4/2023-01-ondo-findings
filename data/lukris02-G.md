# Gas Optimizations Report for Ondo Finance contest
## Overview
During the audit, 2 gas issues were found.  
Total savings ~525.
№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use unchecked blocks for incrementing i | 9 | 315
G-2 | Use unchecked blocks for subtractions where underflow is impossible | 6 | 210

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*9 = 315
#
### G-2. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L297
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L299
-  (previousBalance - balance) https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L865
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L867 
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1281 
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1284

##### Saved
This saves ~35.  
So, ~35*6 = 210