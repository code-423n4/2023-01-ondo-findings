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

or 

```
currentEpoch = block.timestamp / epochDuration;

```

GA9. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L170-L172
Zero amount check need to be performed for these three arguments. 

GA10. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L455
Zero address check is needed for ``_feeReceipient``  to ensure not losing fund to zero address.

GA11. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L465-L471
Zero address check is needed for ``_assetRecipient``  to ensure not losing fund to zero address.

GA12. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L306
This line should be deleted, otherwise it is not consistent with the event ``MintExchangeRateCheckFailed``. 

GA13. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L547
Zero amount check for ``_epochDuration`` is needed.

GA14. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L596
Zero amount check for ``_mintLimit`` is needed.

GA15.
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L610
Zero amount check for ``_RedeemLimit`` is needed.

GA16. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L803-L809
Zero address check for ``newAssetSender`` is needed.
 
GA17. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L949
We need to check that ``msg.value = exCallData[0].value+exCallData[1].value...+exCallData[CallData.length-1].value``. 

GA18. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L80
A range check for ``price`` would be appropriate to prevent price manipulation by a malicous/compromised owner.

GA19. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L106
Zero addess check for ``newOracle`` is needed. 