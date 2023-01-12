**Use  unchecked{++i} / unchecked{i++} where it is not possible to overflow i.e., in loops**

The default "checked" behavior costs more gas when calculating, because under-the-hood checks are implemented in solidity as a series of opcodes that, prior to performing the actual arithmetic, check for under/overflow and revert if it is detected.

In the case of for loops, there is already a condition which prevents under/overflow. Hence, we can use unchecked{} to ignore the internal checks which will save gas in each iteration.

[CashFactory.sol#L127](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127)
[CashKYCSenderFactory.sol#L137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137)
[CashKYCSenderReceiverFactory.sol#L137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137)
[KYCRegistry.sol#L163](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163)
[KYCRegistry.sol#L180](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180)
[CashManager.sol#L750](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750)
[CashManager.sol#L786](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786)
[CashManager.sol#L933](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933)
[CashManager.sol#L961](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961)

