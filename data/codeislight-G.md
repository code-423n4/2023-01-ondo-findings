1. reconstructing CashManager mapping variables

from

```
  // Struct representing all redemption requests in an epoch
  struct RedemptionRequests {
    // Total CASH burned in the epoch
    uint256 totalBurned;
    // Mapping from address to amount of CASH address burned
    mapping(address => uint256) addressToBurnAmt;
  }

  // Mapping from epoch to redemption info struct for that epoch
  mapping(uint256 => RedemptionRequests) public redemptionInfoPerEpoch;

  // Mapping used for getting the exchange rate during a given epoch
  mapping(uint256 => uint256) public epochToExchangeRate;

  // Nested mapping containing mint requests for an epoch
  // { <epoch> : {<user> : <collateralAmount> }
  mapping(uint256 => mapping(address => uint256)) public mintRequestsPerEpoch;
```
to:
```
  // Struct representing all requests in an epoch
    struct Request {
        uint128 mintAmt;
        uint128 burnAmt;
    }
    // Struct representing all information in an epoch
    struct Info {
        uint128 exchangeRate;
        uint128 totalBurned;
    }

    mapping(uint256 => mapping(address => Request)) public requestsPerEpoch;

    mapping(uint256 => Info) public infoPerEpoch;
```

2. CashManager requestManager needs to check if there is any fees for feesInCollateral before transferring.

```
    function requestMint(uint256 collateralAmountIn)
	…
        uint256 feesInCollateral = _getMintFees(collateralAmountIn);
        ...
        if (feesInCollateral > 0)
            collateral.safeTransferFrom(
                msg.sender,
                feeRecipient,
                feesInCollateral
            );
```
3. improve on CashManager.completeRedemptions(), we have a reptetivite loops which can be merged into 1. in essence, there is _check and _process.
Best approach would be to include the code within the check in the process to save on extra unnecessary iterations.
```
    function completeRedemptions(
        address[] calldata redeemers,
        address[] calldata refundees,
        uint256 collateralAmountToDist,
        uint256 epochToService,
        uint256 fees
    ) external override updateEpoch onlyRole(MANAGER_ADMIN) {
        // remove the checks
        ...
        _processRedemption(
            redeemers,
            amountToDist,
            quantityBurned,
            epochToService
        );
        collateral.safeTransferFrom(assetSender, feeRecipient, fees);
        emit RedemptionFeesCollected(feeRecipient, fees, epochToService);
    }
// update _processRefund to check addresses KYC at the beginning
    function _processRefund(
        address[] calldata refundees,
        uint256 epochToService
    ) private returns (uint256 totalCashAmountRefunded) {
        uint256 size = refundees.length;
        for (uint256 i = 0; i < size; ++i) {
            address refundee = refundees[i];
            _checkKYC(refundee); // refundee KYC check
            uint256 cashAmountBurned = redemptionInfoPerEpoch[epochToService]
                .addressToBurnAmt[refundee];
            redemptionInfoPerEpoch[epochToService].addressToBurnAmt[
                refundee
            ] = 0;
            cash.mint(refundee, cashAmountBurned);
            totalCashAmountRefunded += cashAmountBurned;
            emit RefundIssued(refundee, cashAmountBurned, epochToService);
        }
        return totalCashAmountRefunded;
    }
// update _processRedemption to check addresses KYC at the beginning
    function _processRedemption(
        address[] calldata redeemers,
        uint256 amountToDist,
        uint256 quantityBurned,
        uint256 epochToService
    ) private {
        uint256 size = redeemers.length;
        for (uint256 i = 0; i < size; ++i) {
            address redeemer = redeemers[i];
            _checkKYC(redeemer); // redeemer KYC check
            uint256 cashAmountReturned = redemptionInfoPerEpoch[epochToService]
                .addressToBurnAmt[redeemer];
            redemptionInfoPerEpoch[epochToService].addressToBurnAmt[
                redeemer
            ] = 0;
            uint256 collateralAmountDue = (amountToDist * cashAmountReturned) /
                quantityBurned;

            if (collateralAmountDue == 0) {
                revert CollateralRedemptionTooSmall();
            }

            collateral.safeTransferFrom(
                assetSender,
                redeemer,
                collateralAmountDue
            );
            emit RedemptionCompleted(
                redeemer,
                cashAmountReturned,
                collateralAmountDue,
                epochToService
            );
        }
    }
```
4. using delete instead of explicitly resetting value to 0 or false:
instances: 
in CashManager.sol
261:         mintRequestsPerEpoch[epochToClaim][user] = 0;
598:          currentRedeemAmount = 0;
599:          currentMintAmount = 0;
776:          redemptionInfoPerEpoch[epochToService].addressToBurnAmt[
777:              redeemer
778:          ] = 0;
818:          redemptionInfoPerEpoch[epochToService].addressToBurnAmt[
819:                refundee
820:            ] = 0;
in KYCRegistry.sol
181:          kycState[kycRequirementGroup][addresses[i]] = false;

