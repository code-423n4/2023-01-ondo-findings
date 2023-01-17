### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 9*

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
750: for (uint256 i = 0; i < size; ++i)
786: for (uint256 i = 0; i < size; ++i) 
933: for (uint256 i = 0; i < size; ++i)
961: for (uint256 i = 0; i < exCallData.length; ++i)
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
127: for (uint256 i = 0; i < exCallData.length; ++i)
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
137: for (uint256 i = 0; i < exCallData.length; ++i)
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
137: for (uint256 i = 0; i < exCallData.length; ++i)
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
163: for (uint256 i = 0; i < length; i++)
180: for (uint256 i = 0; i < length; i++)
```


### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Number of Instances Identified: 9*

Using the addition operator instead of plus-equals saves **[113 gas]([https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
221: mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
582: currentEpoch += epochDifference;
630: currentMintAmount += collateralAmountIn;
649: currentRedeemAmount += amount;
678-680: redemptionInfoPerEpoch[currentEpoch].addressToBurnAmt[
        msg.sender
      ] += amountCashToRedeem;
681: redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
792: totalCashAmountRefunded += cashAmountBurned;
865: redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
867: redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```


### G-03 REQUIRE or REVERT STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

*Number of Instances Identified: 44*

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
152: require(msg.sender == guardian, "CashFactory: You are not the Guardian");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
163-166: require(
      msg.sender == guardian,
      "CashKYCSenderFactory: You are not the Guardian"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
163-166: require(
      msg.sender == guardian,
      "CashKYCSenderReceiverFactory: You are not the Guardian"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
87: require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
88-91: require(
      !kycState[kycRequirementGroup][user],
      "KYCRegistry: user already verified"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
36-39: require(
      hasRole(TRANSFER_ROLE, _msgSender()), 
      "Cash: must have TRANSFER_ROLE to transfer"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```

63-66: require(
      _getKYCStatus(_msgSender()),//@audit-info use msg.sender
      "CashKYCSender: must be KYC'd to initiate transfer"
    );
70-73: require(
        _getKYCStatus(from),
        "CashKYCSender: `from` address must be KYC'd to send tokens"
      );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
63-66: require(
      _getKYCStatus(_msgSender()),//@audit-info use msg.sender
      "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
    );
70-73: require(
        _getKYCStatus(from),
        "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
      );
78-81: require(
        _getKYCStatus(to),
        "CashKYCSenderReceiver: `to` address must be KYC'd to receive tokens"
      );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```
89: require(msg.sender == owner, "only the owner may call this function.");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
120-123: require(
      CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
      "cToken and fToken must have the same underlying asset if cToken nonzero"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
215-218: require(
      CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
      "cToken and fToken must have the same underlying asset"
    );
280-283: require(
      fTokenToOracleType[fToken] == OracleType.CHAINLINK,
      "fToken is not configured for Chainlink oracle"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```
725-728: require(
      msg.sender == admin,
      "CErc20Delegator::_setImplementation: Caller must be admin"
    );
1251-1254: require(
      msg.value == 0,
      "CErc20Delegator:fallback: cannot send value to fallback"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```
151-154: require(
      msg.sender == admin,
      "cErc20::sweepToken: only admin can sweep tokens"
    );
155-158: require(
      address(token) != underlying,
      "cErc20::sweepToken: can not sweep underlying token"
    );
269-272: require(
      msg.sender == admin,
      "only the admin may set the comp-like delegate"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol

```
30-33: require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"
    );
45-48: require(
      msg.sender == admin,
      "only the admin may call _resignImplementation"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
44: require(msg.sender == admin, "only admin may initialize the market");
45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
52-55: require(
      initialExchangeRateMantissa > 0,
      "initial exchange rate must be greater than zero."
    );
67: require(err == NO_ERROR, "setting interest rate model failed");
584-587: require(
      redeemTokensIn == 0 || redeemAmountIn == 0,
      "one of redeemTokensIn or redeemAmountIn must be zero"
    );
931-934: require(
      amountSeizeError == NO_ERROR,
      "LIQUIDATE_COMPTROLLER_CALCULATE_AMOUNT_SEIZE_FAILED"
    );
1357: require(msg.sender == admin, "Only admin can set KYC requirement group");
1389: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```
151-154: require(
      msg.sender == admin,
      "cErc20::sweepToken: only admin can sweep tokens"
    );
155-158:  require(
      address(token) != underlying,
      "cErc20::sweepToken: can not sweep underlying token"
    );
269-272: require(
      msg.sender == admin,
      "only the admin may set the comp-like delegate"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol

```
30-33: require(
      msg.sender == admin,
      "only the admin may call _becomeImplementation"
    );
45-48: require(
      msg.sender == admin,
      "only the admin may call _resignImplementation"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
44: require(msg.sender == admin, "only admin may initialize the market");
45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
52-55: require(
      initialExchangeRateMantissa > 0,
      "initial exchange rate must be greater than zero."
    );
67: require(err == NO_ERROR, "setting interest rate model failed");
584-587: require(
      redeemTokensIn == 0 || redeemAmountIn == 0,
      "one of redeemTokensIn or redeemAmountIn must be zero"
    );
931-934: require(
      amountSeizeError == NO_ERROR,
      "LIQUIDATE_COMPTROLLER_CALCULATE_AMOUNT_SEIZE_FAILED"
    );
1360: require(msg.sender == admin, "Only admin can set KYC requirement group");
1392: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```


### G-04 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 10*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
119: function _setFTokenToCToken(address fToken, address cToken) internal
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
210: function _setFTokenToCToken(address fToken, address cToken) internal
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
679: function borrowFresh(address payable borrower, uint borrowAmount) internal
870-875: function liquidateBorrowFresh(
    address liquidator,
    address borrower,
    uint repayAmount,
    CTokenInterface cTokenCollateral
  ) internal 
1151-1153: function _setReserveFactorFresh(
    uint newReserveFactorMantissa
  ) internal returns (uint)
1198: function _addReservesFresh(uint addAmount) internal returns (uint, uint)
1253: function _reduceReservesFresh(uint reduceAmount) internal returns (uint)
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
491: function mintFresh(address minter, uint mintAmount) internal
1201: function _addReservesFresh(uint addAmount) internal returns (uint, uint)
1256: function _reduceReservesFresh(uint reduceAmount) internal returns (uint)
```

### G-05 SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

*Number of Instances Identified: 3*

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
292-296: require(
      (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
45-48: require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```


### G-06 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 1*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.
Each MLOAD and MSTORE costs `3 additional gas`

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
781-784: function _processRefund(
    address[] calldata refundees,
    uint256 epochToService
  ) private returns (uint256 totalCashAmountRefunded)
```


### G-07 USE MSG.SENDER INSTEAD OF OPENZEPPELINS \_MSGSENDER() WHEN META-TRANSACTIONS CAPABILITIES ARENT USED

*Number of Instances Identified: 3*

sg.sender` costs 2 gas (CALLER opcode). `_msgSender()` represents the following:

```
function _msgSender() internal view virtual returns (address payable) {  return msg.sender;}
```

When no meta-transactions capabilities are used: `msg.sender` is enough.

See [https://docs.openzeppelin.com/contracts/2.x/gsn](https://docs.openzeppelin.com/contracts/2.x/gsn) for more information about GSN capabilities.

Consider replacing `_msgSender()` with `msg.sender` here:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
37: hasRole(TRANSFER_ROLE, _msgSender()),
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
64: _getKYCStatus(_msgSender()),
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
64: _getKYCStatus(_msgSender()),
```
