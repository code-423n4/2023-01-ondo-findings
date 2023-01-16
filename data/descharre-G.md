## Summary
|ID     | Finding|  Gas saved| Instances |
|:----: | :---           |              :----:    |  :----:         |
|1       |Make for loops unchecked| 414 | 1 |
| 2      |Save storage variable to memory when it's used more than once in a function | 296| 3 |
| 3      |Don't write old value to memory | 500| 25 |
| 4      |Function getBlockNumber is useless | 30| 2 |
| 5      |Don't emit 2 similar events in 1 function | 1289| 2 |
| 7      |Miscellaneous| 4000| 2 |

## Details
## 1 Make for loops unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1. Even if the arrays are extremely long, it will take a massive amount of time and gas to let the for loop overflow.

[KYCRegistry.sol#L163-L165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163-L165)

addKycAdresses function avg gas saved: 414

The more addresses you want to add, the more gas you will save with this gas optimization
```diff
L163
+   uint256 i;
-	for (uint256 i = 0; i < length; i++) {
+   for (; i < lenght; ){ 
		kycState[kycRequirementGroup][addresses[i]] = true;
+	unchecked {
+		i++;
+		}		
	}
```
It can also be done on every other for loop in the project. The larger the array the more gas you will save.

## 2 Save storage variable to memory when it's used more than once in a function
When a storage variable is read for the second time from storage it costs 100 gas. When it's read from memory it only costs 3 gas.
[CashManager.sol#L241-L269](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L241-L269)

claimMint function avg gas saved: 130
```diff
  function claimMint(
    address user,
    uint256 epochToClaim
  ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) {
+   uint256 epochToExchangeRateMemory = epochToExchangeRate[epochToClaim]; 
    uint256 collateralDeposited = mintRequestsPerEpoch[epochToClaim][user];
    if (collateralDeposited == 0) {
      revert NoCashToClaim();
    }
-   if (epochToExchangeRate[epochToClaim] == 0) {
+   if (epochToExchangeRateMemory == 0) {
      revert ExchangeRateNotSet();
    }

    // Get the amount of CASH due at a given rate per epoch
    uint256 cashOwed = _getMintAmountForEpoch(
      collateralDeposited,
      epochToClaim
    );

    mintRequestsPerEpoch[epochToClaim][user] = 0;
    cash.mint(user, cashOwed);

    emit MintCompleted(
      user,
      cashOwed,
      collateralDeposited,
-     epochToExchangeRate[epochToClaim],
+     epochToExchangeRateMemory
      epochToClaim
    );
  }
```

[CashManager.sol#L576-L587](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L576-L587)

transitionEpoch function avg gas saved: 89
```diff
  function transitionEpoch() public {
+   uint256 epochDurationMemory = epochDuration;    
    uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
-      epochDuration;
+      epochDurationMemory 
    if (epochDifference > 0) {
      currentRedeemAmount = 0;
      currentMintAmount = 0;
      currentEpoch += epochDifference;
      currentEpochStartTimestamp =
        block.timestamp -
-       (block.timestamp % epochDuration);
+       (block.timestamp % epochDurationMemory);
    }
  }
```
[OndoPriceOracle.sol#L61-L72](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L61-L72)

getUnderlyingPrice() avg gas saved: 77
```diff
  function getUnderlyingPrice(
    address fToken
  ) external view override returns (uint256) {
+   uint256 price = fTokenToUnderlyingPrice[fToken];
-   if (fTokenToUnderlyingPrice[fToken] != 0) {
-     return fTokenToUnderlyingPrice[fToken];
+   if (price != 0) {
+     return price;
    } else {
      // Price is not manually set, attempt to retrieve price from Compound's
      // oracle
      address cTokenAddress = fTokenToCToken[fToken];
      return cTokenOracle.getUnderlyingPrice(cTokenAddress);
    }
  }
```
Other places where this can be done:
- [CashManager.sol#L281-L319](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L281-L319) lastSetMintExchangeRate: avg gas saved: 544

## 3 Don't write old value to memory
Instead of writing the old value to memory and putting it in the event at the end. You can put the event first so you don't have to write the old value to memory.

Around 20 gas saved per function:
[CashManager.sol#L596-L600](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L596-L600)
```diff
  function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
+   emit MintLimitSet(mintlimit, _mintLimit); 
-   uint256 oldMintLimit = mintLimit;
    mintLimit = _mintLimit;
-   emit MintLimitSet(oldMintLimit, _mintLimit);
  }
```

Can also be done at
- [CashManager.sol#L314-L317](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L314-L317)
- [CashManager.sol#L395-L400](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L395-L400)
- [CashManager.sol#L416-L418](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L416-L418)
- [CashManager.sol#L439-L444](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L439-L444)
- [CashManager.sol#L455-L457](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L455-L457)
- [CashManager.sol#L468-L470](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L468-L470)
- [CashManager.sol#L549-L551](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L549-L551)
- [CashManager.sol#L612-L614](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L612-L614)
- [CashManager.sol#L806-L808](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L806-L808)
- [CashManager.sol#L820-L822](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L820-L822)
- [KYCRegistryClient.sol#L43-L45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L43-L45)
- [KYCRegistryClient.sol#L55-L57](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L55-L57)
- [CTokenCash.sol#L1064-L1066](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1064-L1066)
- [CTokenCash.sol#L1169-L1172](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1169-L1172)
- [CTokenCash.sol#L1368-L1370](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1368-L1370)
- [OndoPriceOracle.sol#L81-L83](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L81-L83)
- [OndoPriceOracle.sol#L96-98](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L96-L98)
- [OndoPriceOracle.sol#L107-L109](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L107-L109)
- [OndoPriceOracleV2.sol#L168-L170](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L168-L170)
- [OndoPriceOracleV2.sol#L183-L185](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L183-L185)
- [OndoPriceOracleV2.sol#L198-L200](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L198-L200)
- [OndoPriceOracleV2.sol#L237-L241](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L237-L241)
- [OndoPriceOracleV2.sol#L312-L317](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L312-L317)
## 4 Function getBlockNumber is useles
The function only returns the blocknumber and does nothing else.

[CTokenModified.sol#L246-L247](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L246-L247)
```solidity
  function getBlockNumber() internal view virtual returns (uint) {
    return block.number;
  }
```
It's cheaper and even shorter to just call block.number directly. Calling block.number directly only costs 2 gas.

Around 30 gas can be saved per function where you apply this.
```diff
L62:
-     accrualBlockNumber = getBlockNumber();
+     accrualBlockNumber = block.number; 
```

Documentation says the function is mainly used for inheriting test contracts to stub the result. If that's the case the function can still exist but don't use the function anywhere else in the contract.

This also applies for [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L246-L248)

## 5 Don't emit 2 similar events in 1 function 
Having a transfer event and a mint event is useless instead use address(0) in the transfer event to make clear it's a mint.

- [CTokenCash.sol#L539](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L539)
- [CTokenModified.sol#L539](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L539)
Mint function gas saved: 1289
```diff
-    emit Mint(minter, actualMintAmount, mintTokens);
-    emit Transfer(address(this), minter, mintTokens);
+    emit Transfer(address(0), minter, mintTokens);
```

Try to minimize emitting events in other functions as well. It costs a lot of gas to emit an event.

## Miscellaneous
### Switch order of enums
[IOndoPriceOracleV2.sol#L71-L76](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L71-L76)

According to the tests, in the function setFTokenToOracleType is the least set to UNINITIALIZED. I don't know if this will occur in real life also. If it does it's better to place the value that gets set the most at the first place in the enum. Writing the first value of an enum to a variable requires way less gas. In the tests this was an average saving of 4000 gas in the setFTokenToOracleType() function with the following. setup.
```diff
  enum OracleType {
+   MANUAL,
    UNINITIALIZED,
-   MANUAL,
    COMPOUND,
    CHAINLINK
  }
```

### Make owner immutable
[JumpRateModelV2.sol#L24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L24)

Owner is only set in the constructor so it's best to make it immutable to save some gas on deployment.