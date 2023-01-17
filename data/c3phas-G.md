### Table of contents
- [FINDINGS](#findings)
- [Using immutable on variables that are only set in the constructor and never after  (2.1k gas per var)](#using-immutable-on-variables-that-are-only-set-in-the-constructor-and-never-after--21k-gas-per-var)
- [Tighly pack storage variables/optimize the order of variable declaration(Gas Savings: 6k in total)](#tighly-pack-storage-variablesoptimize-the-order-of-variable-declarationgas-savings-6k-in-total)
  - [\_notEnteredand  admin can be packed together (Saves 1 SLOT) `Gas savings: 1 * 2k = 2k`](#_notenteredand--admin-can-be-packed-together-saves-1-slot-gas-savings-1--2k--2k)
  - [\_notEntered and admin can be packed together (Saves 1 SLOT) `Gas savings: 1 * 2k = 2k`](#_notentered-and-admin-can-be-packed-together-saves-1-slot-gas-savings-1--2k--2k)
  - [\_notEntered can be packed with an address variable(saves 1 SLOT) `Gas savings: 1 * 2k = 2k`](#_notentered-can-be-packed-with-an-address-variablesaves-1-slot-gas-savings-1--2k--2k)
- [Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard](#massive-15k-per-tx-gas-savings---use-1-and-2-for-reentrancy-guard)
- [The result of a function call should be cached rather than re-calling the function](#the-result-of-a-function-call-should-be-cached-rather-than-re-calling-the-function)
  - [CTokenCash.sol.liquidateBorrowFresh(): getBlockNumber() should be cached](#ctokencashsolliquidateborrowfresh-getblocknumber-should-be-cached)
  - [CTokenModified.sol.liquidateBorrowFresh(): getBlockNumber() should be cached](#ctokenmodifiedsolliquidateborrowfresh-getblocknumber-should-be-cached)
- [Cache the mapping values rather than fetch it every time](#cache-storage-values-in-memory-to-minimize-sloads)
  - [OndoPriceOracle.sol.getUnderlyingPrice(): fTokenToUnderlyingPrice\[fToken\] should be cached](#ondopriceoraclesolgetunderlyingprice-ftokentounderlyingpriceftoken-should-be-cached)
  - [OndoPriceOracleV2.sol.getUnderlyingPrice(): fTokenToUnderlyingPriceCap\[fToken\] should be cached](#ondopriceoraclev2solgetunderlyingprice-ftokentounderlyingpricecapftoken-should-be-cached)
  - [Use the cached value here](#use-the-cached-value-here)
- [Internal/Private functions only called once can be inlined to save gas](#internalprivate-functions-only-called-once-can-be-inlined-to-save-gas)
- [Multiple accesses of a mapping/array should use a local variable cache](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
  - [CashManager.sol.requestRedemption(): redemptionInfoPerEpoch\[currentEpoch\] should be cached in storage](#cashmanagersolrequestredemption-redemptioninfoperepochcurrentepoch-should-be-cached-in-storage)
  - [CashManager.sol.setPendingRedemptionBalance(): redemptionInfoPerEpoch\[epoch\] should be cached in local storage](#cashmanagersolsetpendingredemptionbalance-redemptioninfoperepochepoch-should-be-cached-in-local-storage)
  - [CTokenCash.sol.borrowFresh(): accountBorrows\[borrower\] should be cached in local storage](#ctokencashsolborrowfresh-accountborrowsborrower-should-be-cached-in-local-storage)
  - [CTokenCash.sol.repayBorrowFresh(): accountBorrows\[borrower\] should be cached in local storage](#ctokencashsolrepayborrowfresh-accountborrowsborrower-should-be-cached-in-local-storage)
  - [CTokenModified.sol.borrowFresh(): accountBorrows\[borrower\] should be cached in local storage](#ctokenmodifiedsolborrowfresh-accountborrowsborrower-should-be-cached-in-local-storage)
  - [CTokenModified.sol.repayBorrowFresh(): accountBorrows\[borrower\] should be cached in local storage](#ctokenmodifiedsolrepayborrowfresh-accountborrowsborrower-should-be-cached-in-local-storage)
  - [OndoPriceOracleV2.sol.\_setFTokenToChainlinkOracle(): fTokenToChainlinkOracle\[fToken\] should be cached in local storage](#ondopriceoraclev2sol_setftokentochainlinkoracle-ftokentochainlinkoracleftoken-should-be-cached-in-local-storage)
- [Emitting storage values instead of the memory one.](#emitting-storage-values-instead-of-the-memory-one)
- [Using storage instead of memory for structs/arrays saves gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas)
- [Refactor the code here to avoid storage readings](#refactor-the-code-here-to-avoid-storage-readings)
- [Duplicated require()/revert() checks should be refactored to a modifier or function](#duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function)
- [x += y costs more gas than x = x + y for state variables](#x--y-costs-more-gas-than-x--x--y-for-state-variables)
- [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
- [Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)](#using-unchecked-blocks-to-save-gas---increments-in-for-loop-can-be-unchecked---save-30-40-gas-per-loop-iteration)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [Reorder the require statements to have the less gas consuming before the expensive one](#reorder-the-require-statements-to-have-the-less-gas-consuming-before-the-expensive-one)
- [Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it)](#caching-global-variables-is-more-expensive-than-using-the-actual-variableuse-msgsender-instead-of-caching-it)
- [Use a more recent version of solidity](#use-a-more-recent-version-of-solidity)


## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Throughout the report some places might be denoted with audit tags to show the actual place affected.

## Using immutable on variables that are only set in the constructor and never after  (2.1k gas per var)
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

**Total instaces: 1 `gas savings 1 * 2.1k = 2.1k gas`**

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L24
```solidity
File: /contracts/lending/JumpRateModelV2.sol
24:  address public owner;
```

```diff
diff --git a/contracts/lending/JumpRateModelV2.sol b/contracts/lending/JumpRateModelV2.sol
index a3971c6..d4f2285 100644
--- a/contracts/lending/JumpRateModelV2.sol
+++ b/contracts/lending/JumpRateModelV2.sol
@@ -21,7 +21,7 @@ contract JumpRateModelV2 is InterestRateModel {
   /**
    * @notice The address of the owner, i.e. the Timelock contract, which can update parameters directly
    */
-  address public owner;
+  address public immutable owner;

```

## Tighly pack storage variables/optimize the order of variable declaration(Gas Savings: 6k in total)

Here, the storage variables can be tightly packed to save some slots
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L13-L48
### \_notEnteredand  admin can be packed together (Saves 1 SLOT) `Gas savings: 1 * 2k = 2k`

```solidity
File: /contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

//@audit:   bool internal _notEntered, uint8 public decimals and  address payable public admin can be packed together
contract CTokenStorage {
  bool internal _notEntered;
  string public name;
  string public symbol;
  uint8 public decimals;
  uint internal constant borrowRateMaxMantissa = 0.0005e16;
  uint internal constant reserveFactorMaxMantissa = 1e18;
  address payable public admin;
  address payable public pendingAdmin;
```

```diff
diff --git a/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol b/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
index dd722f4..0ce7402 100644
--- a/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
+++ b/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
@@ -16,6 +16,19 @@ contract CTokenStorage {
    */
   bool internal _notEntered;

+  uint8 public decimals;
+  address payable public admin;
+  address payable public pendingAdmin;
   string public symbol;
-  uint8 public decimals;
   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   uint internal constant reserveFactorMaxMantissa = 1e18;
-  address payable public admin;
-  address payable public pendingAdmin;
```


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L15-L41
### \_notEntered and admin can be packed together (Saves 1 SLOT) `Gas savings: 1 * 2k = 2k`

```solidity
File: /contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
  bool internal _notEntered;
  string public name;
  string public symbol;
  uint8 public decimals;
  uint internal constant borrowRateMaxMantissa = 0.0005e16;
  uint internal constant reserveFactorMaxMantissa = 1e18;
  address payable public admin;
```

```diff
diff --git a/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol b/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
index afffb0f..58af08d 100644
--- a/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
+++ b/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
@@ -13,7 +13,19 @@ contract CTokenStorage {
    * @dev Guard variable for re-entrancy checks
    */
   bool internal _notEntered;
+  uint8 public decimals;
+  address payable public admin;
+  address payable public pendingAdmin;
   string public symbol;
-  uint8 public decimals;
   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   uint internal constant reserveFactorMaxMantissa = 1e18;
-  address payable public admin;
-  address payable public pendingAdmin;
```


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L187-L218
### \_notEntered can be packed with an address variable(saves 1 SLOT) `Gas savings: 1 * 2k = 2k`
```solidity
File: /contracts/lending/tokens/cErc20ModifiedDelegator.sol

//@audit: bool internal _notEntered should be packed with address payable public admin
  bool internal _notEntered;
  string public name;
  string public symbol;
  uint8 public decimals;
  uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
  uint256 internal constant reserveFactorMaxMantissa = 1e18;
  address payable public admin;
```

```diff
diff --git a/contracts/lending/tokens/cErc20ModifiedDelegator.sol b/contracts/lending/tokens/cErc20ModifiedDelegator.sol
index c1e9170..ca1ae31 100644
--- a/contracts/lending/tokens/cErc20ModifiedDelegator.sol
+++ b/contracts/lending/tokens/cErc20ModifiedDelegator.sol

   bool internal _notEntered;
+  uint8 public decimals;
+  address payable public admin;
+  address payable public pendingAdmin;
   string public symbol;
-  uint8 public decimals;
   uint256 internal constant reserveFactorMaxMantissa = 1e18;
-  address payable public admin;
-  address payable public pendingAdmin;
```


## Massive 15k per tx gas savings - use 1 and 2 for Reentrancy guard
Using `true` and `false` will trigger gas-refunds, which after London are 1/5 of what they used to be, meaning using `1` and `2` (keeping the slot non-zero), will cost 5k per change (5k + 5k) vs 20k + 5k, saving you 15k gas per function which uses the modifier

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1434-L1439
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
  modifier nonReentrant() {
    require(_notEntered, "re-entered");
    _notEntered = false;
    _;
    _notEntered = true; // get a gas-refund post-Istanbul
  }
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L1437-L1442
```solidity
File: /contracts/lending/tokens/cToken/CTokenModified.sol
  modifier nonReentrant() {
    require(_notEntered, "re-entered");
    _notEntered = false;
    _;
    _notEntered = true; // get a gas-refund post-Istanbul
  }
```

[See solmate implementation](https://github.com/transmissions11/solmate/blob/main/src/utils/ReentrancyGuard.sol)

We could debate about the above finding being on the c4udit as **using bools** but due to the huge impact it would have , I've highlighted it here. Feel free to not include it when doing gas savings calculations. 


## The result of a function call should be cached rather than re-calling the function

External calls are expensive. Consider caching the following:
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L870-L960
### CTokenCash.sol.liquidateBorrowFresh(): getBlockNumber() should be cached
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
870:  function liquidateBorrowFresh(

889:    if (accrualBlockNumber != getBlockNumber()) { //@audit: Initial call
890:      revert LiquidateFreshnessCheck();
891:    }

894:    if (cTokenCollateral.accrualBlockNumber() != getBlockNumber()) {//@audit: 2nd call
895:      revert LiquidateCollateralFreshnessCheck();
896:    }
```


```diff
diff --git a/contracts/lending/tokens/cCash/CTokenCash.sol b/contracts/lending/tokens/cCash/CTokenCash.sol
index 93d5000..5e6fc1f 100644
--- a/contracts/lending/tokens/cCash/CTokenCash.sol
+++ b/contracts/lending/tokens/cCash/CTokenCash.sol
@@ -886,12 +886,13 @@ abstract contract CTokenCash is
     }

     /* Verify market's block number equals current block number */
-    if (accrualBlockNumber != getBlockNumber()) {
+    uint _getBlockNumber = getBlockNumber();
+    if (accrualBlockNumber != _getBlockNumber) {
       revert LiquidateFreshnessCheck();
     }

     /* Verify cTokenCollateral market's block number equals current block number */
-    if (cTokenCollateral.accrualBlockNumber() != getBlockNumber()) {
+    if (cTokenCollateral.accrualBlockNumber() != _getBlockNumber) {
       revert LiquidateCollateralFreshnessCheck();
     }
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L870-L960
### CTokenModified.sol.liquidateBorrowFresh(): getBlockNumber() should be cached
```solidity
File: /contracts/lending/tokens/cToken/CTokenModified.sol
870:  function liquidateBorrowFresh(

889:    if (accrualBlockNumber != getBlockNumber()) { //@audit: Initial call
890:      revert LiquidateFreshnessCheck();
891:    }

894:    if (cTokenCollateral.accrualBlockNumber() != getBlockNumber()) {//@audit: 2nd call
895:      revert LiquidateCollateralFreshnessCheck();
896:    }
```

## Cache the mapping values rather than fetch it every time


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L61-L72
### OndoPriceOracle.sol.getUnderlyingPrice(): fTokenToUnderlyingPrice[fToken] should be cached
```solidity
File: /contracts/lending/OndoPriceOracle.sol
61:  function getUnderlyingPrice(
62:    address fToken
63:  ) external view override returns (uint256) {
64:    if (fTokenToUnderlyingPrice[fToken] != 0) { //@audit: Initial access
65:      return fTokenToUnderlyingPrice[fToken];//@audit: 2nd access
66:    } else {
```

```diff
diff --git a/contracts/lending/OndoPriceOracle.sol b/contracts/lending/OndoPriceOracle.sol
index 471769e..ddfc781 100644
--- a/contracts/lending/OndoPriceOracle.sol
+++ b/contracts/lending/OndoPriceOracle.sol
@@ -61,8 +61,9 @@ contract OndoPriceOracle is IOndoPriceOracle, Ownable {
   function getUnderlyingPrice(
     address fToken
   ) external view override returns (uint256) {
-    if (fTokenToUnderlyingPrice[fToken] != 0) {
-      return fTokenToUnderlyingPrice[fToken];
+    uint256 _fTokenToUnderlyingPrice = fTokenToUnderlyingPrice[fToken];
+    if (_fTokenToUnderlyingPrice != 0) {
+      return _fTokenToUnderlyingPrice;
     } else {
       // Price is not manually set, attempt to retrieve price from Compound's
       // oracle
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L114-L116
### OndoPriceOracleV2.sol.getUnderlyingPrice(): fTokenToUnderlyingPriceCap\[fToken\] should be cached
```solidity
File: /contracts/lending/OndoPriceOracleV2.sol
114:    if (fTokenToUnderlyingPriceCap[fToken] > 0) { //@audit: Initial access
115:      price = _min(price, fTokenToUnderlyingPriceCap[fToken]);//@audit: 2nd access
116:    }
```

### Use the cached value here 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L50-L55
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
50:    // Set initial exchange rate
51:    initialExchangeRateMantissa = initialExchangeRateMantissa_;//@audit: Cached here
52:    require(
53:      initialExchangeRateMantissa > 0, //@audit: Use the cached value
54:      "initial exchange rate must be greater than zero."
55:    );
```

```diff
diff --git a/contracts/lending/tokens/cCash/CTokenCash.sol b/contracts/lending/tokens/cCash/CTokenCash.sol
index 93d5000..9f0f7da 100644
--- a/contracts/lending/tokens/cCash/CTokenCash.sol
+++ b/contracts/lending/tokens/cCash/CTokenCash.sol
@@ -50,7 +50,7 @@ abstract contract CTokenCash is
     // Set initial exchange rate
     initialExchangeRateMantissa = initialExchangeRateMantissa_;
     require(
-      initialExchangeRateMantissa > 0,
+      initialExchangeRateMantissa_ > 0,
       "initial exchange rate must be greater than zero."
     );
```

## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L119
```solidity
File: /contracts/lending/OndoPriceOracle.sol
119:  function _setFTokenToCToken(address fToken, address cToken) internal {
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L210
```solidity
File: /contracts/lending/OndoPriceOracleV2.sol
210:  function _setFTokenToCToken(address fToken, address cToken) internal {


251:  function _setFTokenToChainlinkOracle(
252:    address fToken,
253:    address chainlinkOracle
254:  ) internal {


324:  function _min(uint256 a, uint256 b) internal pure returns (uint256) {
```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L662-L686
### CashManager.sol.requestRedemption(): redemptionInfoPerEpoch[currentEpoch] should be cached in storage
```solidity
File:/contracts/cash/CashManager.sol
662:  function requestRedemption(

678:    redemptionInfoPerEpoch[currentEpoch].addressToBurnAmt[
679:        msg.sender
680:      ] += amountCashToRedeem;
681:    redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L851-L876
### CashManager.sol.setPendingRedemptionBalance(): redemptionInfoPerEpoch[epoch] should be cached in local storage
```solidity
File:/contracts/cash/CashManager.sol
851:  function setPendingRedemptionBalance(

859:    uint256 previousBalance = redemptionInfoPerEpoch[epoch].addressToBurnAmt[
860:      user
861:    ];

864:    if (balance < previousBalance) {
865:      redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
866:    } else if (balance > previousBalance) {
867:      redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
868:    }
869:    redemptionInfoPerEpoch[epoch].addressToBurnAmt[user] = balance;
870:    emit PendingRedemptionBalanceSet(
871:      user,
872:      epoch,
873:      balance,
874:      redemptionInfoPerEpoch[epoch].totalBurned
875:    );
876:  }
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L720-L721
### CTokenCash.sol.borrowFresh(): accountBorrows[borrower] should be cached in local storage
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
720:    accountBorrows[borrower].principal = accountBorrowsNew;//@audit: Initial access
721:    accountBorrows[borrower].interestIndex = borrowIndex;//@audit: 2nd access
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L822-L823
### CTokenCash.sol.repayBorrowFresh(): accountBorrows[borrower] should be cached in local storage
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
822:    accountBorrows[borrower].principal = accountBorrowsNew; //@audit: Initial access
823:    accountBorrows[borrower].interestIndex = borrowIndex;//@audit: 2nd access
```


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L720-L721
### CTokenModified.sol.borrowFresh(): accountBorrows[borrower] should be cached in local storage
```solidity
File:/contracts/lending/tokens/cToken/CTokenModified.sol
720:    accountBorrows[borrower].principal = accountBorrowsNew;//@audit: Initial access
721:    accountBorrows[borrower].interestIndex = borrowIndex;//@audit: 2nd access
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L822-L823
### CTokenModified.sol.repayBorrowFresh(): accountBorrows[borrower] should be cached in local storage
```solidity
File:/contracts/lending/tokens/cToken/CTokenModified.sol
822:   accountBorrows[borrower].principal = accountBorrowsNew;//@audit: Initial access
823:   accountBorrows[borrower].interestIndex = borrowIndex;//@audit: 2nd access
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L251-L267
### OndoPriceOracleV2.sol.\_setFTokenToChainlinkOracle(): fTokenToChainlinkOracle\[fToken] should be cached in local storage
```solidity
File: /contracts/lending/OndoPriceOracleV2.sol
251:  function _setFTokenToChainlinkOracle(

260:    fTokenToChainlinkOracle[fToken].scaleFactor = (10 **
261:      (36 -
262:        uint256(IERC20Like(underlying).decimals()) -
263:        uint256(AggregatorV3Interface(chainlinkOracle).decimals()))); //@audit: Initial access
264:    fTokenToChainlinkOracle[fToken].oracle = AggregatorV3Interface(
265:      chainlinkOracle
266:    );//@audit: 2nd access
267:  }
```

## Emitting storage values instead of the memory one.
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L171-L190

```solidity
File: /contracts/lending/JumpRateModelV2.sol
 171: function updateJumpRateModelInternal(

182:    kink = kink_;

184:    emit NewInterestParams(
185:      baseRatePerBlock,
186:      multiplierPerBlock,
187:      jumpMultiplierPerBlock,
188:      kink
189:    );
190:  }
```

```diff
diff --git a/contracts/lending/JumpRateModelV2.sol b/contracts/lending/JumpRateModelV2.sol
index a3971c6..cc525bb 100644
--- a/contracts/lending/JumpRateModelV2.sol
+++ b/contracts/lending/JumpRateModelV2.sol
@@ -185,7 +185,7 @@ contract JumpRateModelV2 is InterestRateModel {
       baseRatePerBlock,
       multiplierPerBlock,
       jumpMultiplierPerBlock,
-      kink
+      kink_
     );
   }
 }
```

**Other instances**

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L817-L823

```solidity
File: /contracts/cash/CashManager.sol

//@audit: we should emit newRedeemMinimum instead of minimumRedeemAmount
817:  function setRedeemMinimum(
818:    uint256 newRedeemMinimum
819:  ) external onlyRole(MANAGER_ADMIN) {
820:    uint256 oldRedeemMin = minimumRedeemAmount;
821:    minimumRedeemAmount = newRedeemMinimum;
822:    emit MinimumRedeemAmountSet(oldRedeemMin, minimumRedeemAmount);
823:  }
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L720-L747
```solidity
File: /contracts/lending/tokens/cErc20ModifiedDelegator.sol

//@audit: We should emit implementation_ instead of implementation
720:  function _setImplementation(
721:    address implementation_,
722:    bool allowResign,
723:    bytes memory becomeImplementationData
724:  ) public {

736:    address oldImplementation = implementation;
737:    implementation = implementation_;

746:    emit NewImplementation(oldImplementation, implementation);
747:  }
```

## Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L284
```solidity
File: /contracts/lending/OndoPriceOracleV2.sol
284:    ChainlinkOracleInfo memory chainlinkInfo = fTokenToChainlinkOracle[fToken];
```


## Refactor the code here to avoid storage readings
**Note: I've added some explanations as to how/why this would work**

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L1256-L1295

**1st instance**
```solidity
File: /contracts/lending/tokens/cToken/CTokenModified.sol
  function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {

    // Check caller is admin
    if (msg.sender != admin) {
      revert ReduceReservesAdminCheck();
    }

    doTransferOut(admin, reduceAmount);

    emit ReservesReduced(admin, reduceAmount, totalReservesNew);
```

The only way we get to `doTransferOut(admin, reduceAmount);` is if the `msg.sender` is equal to `admin`, therefore rather than use `admin` (storage variable) in the function call `doTransferOut(admin, reduceAmount);` we could use the cheaper `msg.sender`. Similar to the emit line , we could just emit `msg.sender`

```diff
diff --git a/contracts/lending/tokens/cToken/CTokenModified.sol b/contracts/lending/tokens/cToken/CTokenModified.sol
index 8798b90..45b24da 100644
--- a/contracts/lending/tokens/cToken/CTokenModified.sol
+++ b/contracts/lending/tokens/cToken/CTokenModified.sol
@@ -1287,9 +1287,9 @@ abstract contract CTokenModified is
     totalReserves = totalReservesNew;

     // doTransferOut reverts if anything goes wrong, since we can't be sure if side effects occurred.
-    doTransferOut(admin, reduceAmount);
+    doTransferOut(payable(msg.sender), reduceAmount);

-    emit ReservesReduced(admin, reduceAmount, totalReservesNew);
+    emit ReservesReduced(msg.sender, reduceAmount, totalReservesNew);

     return NO_ERROR;
   }
```

**2nd instance**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1253-L1292
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
1253:  function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {

1257:    // Check caller is admin
1258:    if (msg.sender != admin) {
1259:      revert ReduceReservesAdminCheck();
1260:    }

1287:    doTransferOut(admin, reduceAmount);//@audit: use msg.sender here instead of admin

1289:    emit ReservesReduced(admin, reduceAmount, totalReservesNew);//@audit: use msg.sender here 
```

**3rd instance**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L150-L161
```solidity
File:/contracts/lending/tokens/cCash/CCash.sol
150:  function sweepToken(EIP20NonStandardInterface token) external override {
151:   require(
152:      msg.sender == admin,
153:      "cErc20::sweepToken: only admin can sweep tokens"
154:    );

160:    token.transfer(admin, balance);
161:  }
```

Since we are checking that `msg.sender == admin`, it means the only way we get to line 160 is if the two are equal thus we can just use `msg.sender`(global variable - cheaper)  in `token.transfer(admin, balance);` rather than use `admin`(storage variable - expensive )

```diff
diff --git a/contracts/lending/tokens/cCash/CCash.sol b/contracts/lending/tokens/cCash/CCash.sol
index 996283d..9f83506 100644
--- a/contracts/lending/tokens/cCash/CCash.sol
+++ b/contracts/lending/tokens/cCash/CCash.sol
@@ -157,7 +157,7 @@ contract CCash is CTokenCash, CErc20Interface {
       "cErc20::sweepToken: can not sweep underlying token"
     );
     uint256 balance = token.balanceOf(address(this));
-    token.transfer(admin, balance);
+    token.transfer(payable(msg.sender), balance);
   }
```

**4th instance**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L30-L56
```solidity
File: /contracts/lending/tokens/cCash/CCash.sol
30:  function initialize(

53:    // Set underlying and sanity check it
54:    underlying = underlying_; 
55:    EIP20Interface(underlying).totalSupply();
56:  }
```

As `underlying` (storage variable) is equal to `underlying_` (local variable) it would be cheaper to just read the local variable here `EIP20Interface(underlying).totalSupply();`

```diff
diff --git a/contracts/lending/tokens/cCash/CCash.sol b/contracts/lending/tokens/cCash/CCash.sol
index 996283d..8e30298 100644
--- a/contracts/lending/tokens/cCash/CCash.sol
+++ b/contracts/lending/tokens/cCash/CCash.sol
@@ -52,7 +52,7 @@ contract CCash is CTokenCash, CErc20Interface {

     // Set underlying and sanity check it
     underlying = underlying_;
-    EIP20Interface(underlying).totalSupply();
+    EIP20Interface(underlying_).totalSupply();
   }
```

**5th instance**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L30-L56
```solidity
File: /contracts/lending/tokens/cToken/CErc20.sol
30:  function initialize(

53:    // Set underlying and sanity check it
54:    underlying = underlying_; 
55:    EIP20Interface(underlying).totalSupply();
56:  }
```

```diff
diff --git a/contracts/lending/tokens/cToken/CErc20.sol b/contracts/lending/tokens/cToken/CErc20.sol
index 6998c56..3bb0011 100644
--- a/contracts/lending/tokens/cToken/CErc20.sol
+++ b/contracts/lending/tokens/cToken/CErc20.sol
@@ -52,7 +52,7 @@ contract CErc20 is CTokenModified, CErc20Interface {

     // Set underlying and sanity check it
     underlying = underlying_;
-    EIP20Interface(underlying).totalSupply();
+    EIP20Interface(underlying_).totalSupply();
   }
```

**6th instance**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L150-L161
```solidity
File:/contracts/lending/tokens/cToken/CErc20.sol
150:  function sweepToken(EIP20NonStandardInterface token) external override {
151:   require(
152:      msg.sender == admin,
153:      "cErc20::sweepToken: only admin can sweep tokens"
154:    );

160:    token.transfer(admin, balance);
161:  }
```
The require statement ensures that `msg.sender == admin` therefore we can use `msg.sender` (global variable - cheap) in `token.transfer(admin, balance)` rather than `admin` (storage - expensive)

```diff
diff --git a/contracts/lending/tokens/cToken/CErc20.sol b/contracts/lending/tokens/cToken/CErc20.sol
index 6998c56..2684cbf 100644
--- a/contracts/lending/tokens/cToken/CErc20.sol
+++ b/contracts/lending/tokens/cToken/CErc20.sol
@@ -157,7 +157,7 @@ contract CErc20 is CTokenModified, CErc20Interface {
       "cErc20::sweepToken: can not sweep underlying token"
     );
     uint256 balance = token.balanceOf(address(this));
-    token.transfer(admin, balance);
+    token.transfer(payable(msg.sender), balance);
   }
```

## Duplicated require()/revert() checks should be refactored to a modifier or function
This saves deployement gas

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L151-L154
```solidity
File: /contracts/lending/tokens/cCash/CCash.sol
151:    require(
152:      msg.sender == admin,
153:      "cErc20::sweepToken: only admin can sweep tokens"
154:    );
```

The above check is also repeated on [Line 269](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L269-L272)

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L151-L154
```solidity
File: /contracts/lending/tokens/cToken/CErc20.sol
151:    require(
152:      msg.sender == admin,
153:      "cErc20::sweepToken: only admin can sweep tokens"
154:    );
```

Repeated on the following:

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L269-L272

**Other instances**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L30-L33
```solidity
File: /contracts/lending/tokens/cCash/CCashDelegate.sol
30:    require(
31:      msg.sender == admin,
32:      "only the admin may call _becomeImplementation"
33:    );

45:    require(
46:      msg.sender == admin,
47:      "only the admin may call _resignImplementation"
48:    );
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenDelegate.sol#L30-L33
```solidity
File: /contracts/lending/tokens/cToken/CTokenDelegate.sol
30:    require(
31:      msg.sender == admin,
32:      "only the admin may call _becomeImplementation"
33:    );

45:    require(
46:      msg.sender == admin,
47:      "only the admin may call _resignImplementation"
48:    );
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L44
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
44:    require(msg.sender == admin, "only admin may initialize the market");
```
We can have a modifier that checks that the msg.sender is the admin. Unless we really need to have different error messages we could generalize the errors for all functions that expect to be called by an admin. 

The above check or a variation of it is found on the following lines

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1064-L1066
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
1064:    if (msg.sender != admin) {
1065:      revert SetPendingAdminOwnerCheck();
1066:    }


1116:    if (msg.sender != admin) {
1117:      revert SetComptrollerOwnerCheck();
1118:    }


1155:    if (msg.sender != admin) {
1156:      revert SetReserveFactorAdminCheck();
1157:    }

1258:    if (msg.sender != admin) {
1259:      revert ReduceReservesAdminCheck();
1260:    }

1321:    if (msg.sender != admin) {
1322:      revert SetInterestRateModelOwnerCheck();
1323:    }

1357:    require(msg.sender == admin, "Only admin can set KYC requirement group");

1379:    require(msg.sender == admin, "Only admin can set KYC registry");
```


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L44
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol

44:    require(msg.sender == admin, "only admin may initialize the market");

1064:    if (msg.sender != admin) {
1065:      revert SetPendingAdminOwnerCheck();
1066:    }

1116:    if (msg.sender != admin) {
1117:      revert SetComptrollerOwnerCheck();
1118:    }

1155:    if (msg.sender != admin) {
1156:      revert SetReserveFactorAdminCheck();
1157:    }

1261:    if (msg.sender != admin) {
1262:      revert ReduceReservesAdminCheck();
1263:    }

1324:    if (msg.sender != admin) {
1325:      revert SetInterestRateModelOwnerCheck();
1326:    }

1360:    require(msg.sender == admin, "Only admin can set KYC requirement group");

1382:    require(msg.sender == admin, "Only admin can set KYC registry");
```

## x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L582
**Saves 24 Gas on average**

|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 1176    | 1420   | 1469 | 1469 |
| After  | 1156    | 1396   | 1444 | 1444 |

```solidity
File: /contracts/cash/CashManager.sol
582:      currentEpoch += epochDifference;
```

```diff
diff --git a/contracts/cash/CashManager.sol b/contracts/cash/CashManager.sol
index 4eb4203..3156721 100644
--- a/contracts/cash/CashManager.sol
+++ b/contracts/cash/CashManager.sol
@@ -579,7 +579,7 @@ contract CashManager is
     if (epochDifference > 0) {
       currentRedeemAmount = 0;
       currentMintAmount = 0;
-      currentEpoch += epochDifference;
+      currentEpoch = currentEpoch + epochDifference;
       currentEpochStartTimestamp =
         block.timestamp -
         (block.timestamp % epochDuration);
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L630
```solidity
File: /contracts/cash/CashManager.sol
630:    currentMintAmount += collateralAmountIn;

649:    currentRedeemAmount += amount;
```

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L297
```solidity
File: /contracts/cash/CashManager.sol
297:      rateDifference = exchangeRate - lastSetMintExchangeRate;
```

The operation `exchangeRate - lastSetMintExchangeRate` cannot underflow as it would only be evaluated if `exchangeRate` is greater than `lastSetMintExchangeRate`

We can modify it as follows

```diff
diff --git a/contracts/cash/CashManager.sol b/contracts/cash/CashManager.sol
index 4eb4203..a99cea7 100644
--- a/contracts/cash/CashManager.sol
+++ b/contracts/cash/CashManager.sol
@@ -294,7 +294,10 @@ contract CashManager is

     uint256 rateDifference;
     if (exchangeRate > lastSetMintExchangeRate) {
-      rateDifference = exchangeRate - lastSetMintExchangeRate;
+      unchecked {
+        rateDifference = exchangeRate - lastSetMintExchangeRate;
+      }
+
     } 
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L299
```solidity
File: /contracts/cash/CashManager.sol
299:      rateDifference = lastSetMintExchangeRate - exchangeRate;
```
The operation `lastSetMintExchangeRate - exchangeRate` cannot underflow as it would only be evaluated if `lastSetMintExchangeRate` is greater than `exchangeRate`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L865
```solidity
File: /contracts/cash/CashManager.sol
865:      redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
```
The operation `previousBalance - balance` cannot underflow as it would only be evaluated if `previousBalance` is greater than `balance` due to the check on [Line 864](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L864)

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L865
```solidity
File: /contracts/cash/CashManager.sol
867:      redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```
The operation `balance - previousBalance` cannot underflow as it would only be evaluated if `balance` is greater than `previousBalance` due to the check on [Line 866](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L866)


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1281
```solidity
File: /contracts/cash/CashManager.sol
1281:    totalReservesNew = totalReserves - reduceAmount;
```

The operation `totalReserves - reduceAmount` cannot underflow due to the check on [Line 1273](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1273) that ensures that **totalReserves** is greater than **reduceAmount** before performing the arithemtic operation

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L1284
```solidity
File: /contracts/cash/CashManager.sol
1284:    totalReservesNew = totalReserves - reduceAmount;
```
The operation `totalReserves - reduceAmount` cannot underflow due to the check on [Line 1276](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1276) that ensures that **totalReserves** is greater than **reduceAmount** before performing the arithemtic operation

## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L127-L133
```solidity
File: /contracts/cash/factory/CashFactory.sol
127:    for (uint256 i = 0; i < exCallData.length; ++i) {
128:      (bool success, bytes memory ret) = address(exCallData[i].target).call{
129:        value: exCallData[i].value
130:      }(exCallData[i].data);
131:      require(success, "Call Failed");
132:      results[i] = ret;
133:    }
```
The above should be modified to:

```diff
diff --git a/contracts/cash/factory/CashFactory.sol b/contracts/cash/factory/CashFactory.sol
index 24b67ba..84bb244 100644
--- a/contracts/cash/factory/CashFactory.sol
+++ b/contracts/cash/factory/CashFactory.sol
@@ -124,12 +124,15 @@ contract CashFactory is IMulticall {
     ExCallData[] calldata exCallData
   ) external payable override onlyGuardian returns (bytes[] memory results) {
     results = new bytes[](exCallData.length);
-    for (uint256 i = 0; i < exCallData.length; ++i) {
+    for (uint256 i = 0; i < exCallData.length;) {
       (bool success, bytes memory ret) = address(exCallData[i].target).call{
         value: exCallData[i].value
       }(exCallData[i].data);
       require(success, "Call Failed");
       results[i] = ret;
+      unchecked {
+        ++i;
+      }
     }
   }
```

**Other Instances to modify**

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L137
```solidity
File: /contracts/cash/factory/CashKYCSenderFactory.sol
137:    for (uint256 i = 0; i < exCallData.length; ++i) {
```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137
```solidity
File: /contracts/cash/factory/CashKYCSenderReceiverFactory.sol
137:    for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L163
```solidity
File: /contracts/cash/kyc/KYCRegistry.sol
163:    for (uint256 i = 0; i < length; i++) {

180:    for (uint256 i = 0; i < length; i++) {

```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L750
```solidity
File: /contracts/cash/CashManager.sol
750:    for (uint256 i = 0; i < size; ++i) {

786:    for (uint256 i = 0; i < size; ++i) {

933:    for (uint256 i = 0; i < size; ++i) {

961:    for (uint256 i = 0; i < exCallData.length; ++i) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)


## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&


After splitting the require statement
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require


https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L292-L296
```solidity
File: /contracts/lending/OndoPriceOracleV2.sol
292:    require(
293:      (answeredInRound >= roundId) &&
294:        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
295:      "Chainlink oracle price is stale"
296:    );
```

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L45-L48
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
45:    require(
46:      accrualBlockNumber == 0 && borrowIndex == 0,
47:      "market may only be initialized once"
48:    );
```

## Reorder the require statements to have the less gas consuming before the expensive one
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L79-L112
**Save 225 gas on average**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 752    | 23892   | 34436 | 41128 |
| After  | 690    | 23667   | 34436 | 41128 |

```solidity
File: /contracts/cash/kyc/KYCRegistry.sol
79: function addKYCAddressViaSignature(
80:    uint256 kycRequirementGroup,
81:    address user,
82:    uint256 deadline,
83:    uint8 v,
84:    bytes32 r,
85:    bytes32 s
86:  ) external {
87:    require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
88:    require(
89:      !kycState[kycRequirementGroup][user],
90:      "KYCRegistry: user already verified"
91:    );
92:    require(block.timestamp <= deadline, "KYCRegistry: signature expired");
```

Its cheaper to check for `block.timestamp <= deadline` as compared to `!kycState\[kycRequirementGroup]\[user]` as this involves reading the storage variable. Therefore if the `require(block.timestamp <= deadline, "KYCRegistry: signature expired");` fails it would be cheaper to fail before evaluating the `!kycState\[kycRequirementGroup]\[user]`


```diff
diff --git a/contracts/cash/kyc/KYCRegistry.sol b/contracts/cash/kyc/KYCRegistry.sol
index 896c727..d5401df 100644
--- a/contracts/cash/kyc/KYCRegistry.sol
+++ b/contracts/cash/kyc/KYCRegistry.sol
@@ -85,11 +85,12 @@ contract KYCRegistry is AccessControlEnumerable, IKYCRegistry, EIP712 {
     bytes32 s
   ) external {
     require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
+    require(block.timestamp <= deadline, "KYCRegistry: signature expired");
+
     require(
       !kycState[kycRequirementGroup][user],
       "KYCRegistry: user already verified"
     );
-    require(block.timestamp <= deadline, "KYCRegistry: signature expired");
     bytes32 structHash = keccak256(
       abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
     );
```

## Caching global variables is more expensive than using the actual variable(use msg.sender instead of caching it)

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L182-L190
It's cheaper to use msg.sender as compared to caching 
```solidity
File: /contracts/lending/tokens/cCash/CTokenCash.sol
182:  function approve(
183:    address spender,
184:    uint256 amount
185:  ) external override returns (bool) {
186:    address src = msg.sender;
187:    transferAllowances[src][spender] = amount;
188:    emit Approval(src, spender, amount);
189:    return true;
190:  }
```

```diff
diff --git a/contracts/lending/tokens/cCash/CTokenCash.sol b/contracts/lending/tokens/cCash/CTokenCash.sol
index 93d5000..2dbaadd 100644
--- a/contracts/lending/tokens/cCash/CTokenCash.sol
+++ b/contracts/lending/tokens/cCash/CTokenCash.sol
@@ -183,9 +183,8 @@ abstract contract CTokenCash is
     address spender,
     uint256 amount
   ) external override returns (bool) {
-    address src = msg.sender;
-    transferAllowances[src][spender] = amount;
-    emit Approval(src, spender, amount);
+    transferAllowances[msg.sender][spender] = amount;
+    emit Approval(msg.sender, spender, amount);
     return true;
   }
```

## Use a more recent version of solidity

Use a solidity version of at least 0.8 to get default underflow/overflow checks, use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

We can avoid using the library safeMath in the following file by using version 0.8+

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L1
```solidity
File: /contracts/lending/JumpRateModelV2.sol
1:pragma solidity ^0.5.16;
```
