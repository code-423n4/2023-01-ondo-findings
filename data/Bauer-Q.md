## No check mint fee is 0
CashManager.sol
If the mintFee is not setted of 0 , the  ```collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);```  will transfer 0 amount to feeRecipient in the requestMint(), this will waste a lot of gas.

```
function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
    if (collateralAmountIn < minimumDepositAmount) {
      revert MintRequestAmountTooSmall();
    }

    uint256 feesInCollateral = _getMintFees(collateralAmountIn);
    uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral;

    _checkAndUpdateMintLimit(depositValueAfterFees);

    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);
    collateral.safeTransferFrom(
      msg.sender,
      assetRecipient,
      depositValueAfterFees
    );

    mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;

    emit MintRequested(
      msg.sender,
      currentEpoch,
      collateralAmountIn,
      depositValueAfterFees,
      feesInCollateral
    );
  }

```
## Recommended Mitigation Steps：
```
if (feesInCollateral != 0)
    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);

```

