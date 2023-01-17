1. 
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L626
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L630
'currentMintAmount' is read twice in the whole of _checkAndUpdateMintLimit() function.

2.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L645
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L649
'currentRedeemAmount' is read twice in the whole of _checkAndUpdateRedeemLimit() function.

3.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L822
MinimumRedeemAmountSet could emits a local variable 'newRedeemMinimum' instead of a storage variable 'minimumRedeemAmount'.

4.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L865
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L867
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L874
'redemptionInfoPerEpoch[epoch].totalBurned' is read twice in the whole of setPendingRedemptionBalance() function.

5.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L53
It could check a local variable 'initialExchangeRateMantissa_' instead of a storage variable 'initialExchangeRateMantissa'.

6.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L24
'owner' could be made as immutable.
