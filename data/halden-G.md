## [G-01] Use assembly to check for address(0). Missed in the C4udit output
File CashManager.sol: [141](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L141), [144](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L144), [147](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L147), [150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L150), [150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L150)

File KYCRegistryClient.sol: [40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L40)

File CashKYCSender.sol: [68](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L68)

File CashKYCSenderReceiver.sol: [68](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L68), [76](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L76)

File KYCRegistryClient.sol: [40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L40)


## [G-02] Cache storage values in memory to minimize SLOADs
The code can be optimized by minimising the number of SLOADs. Storage value should get cached in memory if they occur more than once.

1) Use `_epochDuration` instead of `epochDuration` [176](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L176)
Recommended:
```
(block.timestamp % _epochDuration);
```
2) Cache `epochToExchangeRate[epochToClaim] ` in memory. If the check for equal to zero is false we can save 2 SLOAD. One in line 266 and another in 492. [249](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L249)
Recommended:
```  
uint256 rate = epochToExchangeRate[epochToClaim];
if (rate == 0) { // line 249
     revert ExchangeRateNotSet();
}

emit MintCompleted(
    user,
    cashOwed,
    collateralDeposited,
    rate // line 266
    epochToClaim
);

function _getMintAmountForEpoch(
    uint256 collateralAmountIn,
    uint256 rate // line 489
  ) private view returns (uint256 cashAmountOut) {
    uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;
    cashAmountOut = amountE24 / rate; // line 492
}
```
3) Cache `currentEpoch` in memory. [678](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L678)
Recommended:
```
    uint256 epoch = currentEpoch;
    redemptionInfoPerEpoch[epoch].addressToBurnAmt[  // line 678
        msg.sender
      ] += amountCashToRedeem;
    redemptionInfoPerEpoch[epoch].totalBurned += amountCashToRedeem; // line 681


    emit RedemptionRequested(msg.sender, amountCashToRedeem, epoch); // line 685
```
4) Cache `feeRecipient` in memory. [725](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L725)
Recommended:
```
    address recipient = feeRecipient;
    collateral.safeTransferFrom(assetSender, recipient, fees); // line 725
    emit RedemptionFeesCollected(recipient, fees, epochToService); // line 726
```
5) Use memory variable in emmiting of event [822](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L822)
Recommended:
```
emit MinimumRedeemAmountSet(oldRedeemMin, newRedeemMinimum);
```
6) Cache `fTokenToUnderlyingPrice[fToken]` in memory
Recomended:
```
    uint256 underlyingPrice = fTokenToUnderlyingPrice[fToken];
    if (underlyingPrice != 0) { // Line 64
      return underlyingPrice; // Line 65
    } else {
      address cTokenAddress = underlyingPrice; // Line 69
```
## [G-03] Add unchecked {} where the operands can not underflow/overflow because of a previous check
[210](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L210) `feesInCollateral` every time will be less than `collateralAmountIn`. 
[296-300](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L296-L300)
[626](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L626)
[645](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L645)
[722](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L722) `refundedAmt` can be equal to `redemptionInfoPerEpoch[epochToService].totalBurned ` or less
[723](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L723)  In the comment above function we know that `collateralAmountToDist` include total amount to distribute for redemptions and fees to accrue to Ondo. Based on the comment, the variable `fees` will be less than `collateralAmountToDist`. So we can add  unchecked {}  block because operands can not underflow. Also I will recomend to be added additional check ``collateralAmountToDist`` > ``fees` as a require in the start of function. If this conditional is false the function should revert. 
[865](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L865)
[867](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L867)
## [G-04] Using unchecked blocks to save gas - Increments in for loop can be unchecked
File CashManager.sol: [750](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750), [786](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786), [933](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933), [961](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961)

File CashFactory.sol: [127](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127)

File CashKYCSenderFactory.sol: [137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137)

File CashKYCSenderReceiverFactory.sol: [137](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashKYCSenderReceiverFactory/CashFactory.sol#L137)

## [G-05] Splitting require() statements that use && saves gas
File OndoPriceOracleV2.sol: [293](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L293)

## [G-06] Use Custom Errors. Missed in the C4udit output
File Cash.sol: [37](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L37)

File CCashDelegate.sol: [31](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L31), [46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L46
)

File CTokenDelegate.sol: [31](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L31), [46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L46)

File CashKYCSender.sol [64](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L64), [71](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L71)

File OndoPriceOracle.sol [121](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L121)

File CashKYCSenderReceiver.sol: [64](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L64), [71](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L71), [79](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L79)

File KYCRegistry.sol [89](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L89)

## [G-07] require()/revert() strings longer than 32 bytes cost extra gas
File Cash.sol: [37](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L37)

File CCashDelegate.sol: [32](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L32), [47](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L47)
)

File CTokenDelegate.sol: [32](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L32), [47](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L47)

File CashKYCSender.sol [65](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L65), [72](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L72)

File OndoPriceOracle.sol [122](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L122)

File CashKYCSenderReceiver.sol: [65](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L65), [72](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L72), [80](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L80)

File CashFactory.sol: [152](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L152)

File KYCRegistry.sol [90](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L90)
## [GAS-8] Use calldata instead of memory for function arguments that do not get mutated. Missed in the C4udit output
File CashKYCSender.sol: [47-48](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L47-L48)

File CashKYCSenderReceiver.sol: [47-48](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L47-L48)