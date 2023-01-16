GA1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L576-L587
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L584-L587
We need to check that only one of ``redeemTokensIn`` and ``redeemAmountIn`` is greater than zero, and the other is equal to zero.
```
require(
      (redeemTokensIn >0 && redeemAmountIn == 0) || (redeemTokensIn == 0 && redeemAmountIn > 0),
      "Only one of redeemTokensIn and redeemAmountIn must be zero"
    );
```

GA2. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L650
We need to track the actual doTransferOut() since their might be a discrepancy, either we revert when there is a discrepancy, or we need to let the caller know the slippage and let the caller decides what to do. 

```
actualRedeemAmount = doTransferOut(redeemer, redeemAmount);

```
GA3. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L795
The documentation should be "/* If repayAmount == max, repayAmount = accountBorrows */

GA4: https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L94
Zero amount check for ``tokens`` is needed.


GA5. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L479
Zero amount check for ``mintAmount`` is needed.

GA6. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2
Lock all contracts at the most recent version of solidity 0.8.17.

GA7. https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2
The name of the file is not consistent with the name of the contract. Also, ``CTokenInterfacesModifiedCash.sol`` is misleading if it is NOT an interface.

GA8. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115
There should be a way to change ``protocolSeizeShareMantissa``. 

GA8. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L168
This line is either unnecessary since it does nothing or it is a typo that need to be revised as follows:
```
currentEpoch = _currentEpoch;

```

