## 1.No check mint fee is 0
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


## 2. whenPaused is useless in function call multiexcall()
CashManager.sol
Three functions requestMint(),claimMint(),requestRedemption() have the not pause modifier. There is whenPaused  modifier for function call multiexcall(), it means the multiexcall() can be trigger when the status is Paused, however  at this point  MANAGER_ADMIN stile not able to call function that is modified by not paused.

```
function multiexcall(
    ExCallData[] calldata exCallData
  )
    external
    payable
    override
    nonReentrant
    onlyRole(MANAGER_ADMIN)
    whenPaused
    returns (bytes[] memory results)
  {
    results = new bytes[](exCallData.length);
    for (uint256 i = 0; i < exCallData.length; ++i) {
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
      require(success, "Call Failed");
      results[i] = ret;
    }
  }

```





