## Use of `safeMath` in `pragma ^0.8.0`
### Summary
`SafeMath` is used but it's not needed anymore

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/JumpRateModelV2.sol#L4
`import "./compound/SafeMath.sol";`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/JumpRateModelV2.sol#L12
`using SafeMath for uint;`

### Mitigation
`SafeMath` can be not used as version `pragma ^0.8.0` avoiding a library deployment / calls to it


## Increments can be `unchecked` in loops
### Summary
Unchecked operations as the `++i` on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

```diff
-    for (uint256 i; i < numIterations; i++) {
+    for (uint256 i; i < numIterations;) {
      // ...
+      unchecked { ++i; }
    // The risk of overflow is inexistent for a uint256 here.
    }
```

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/factory/CashFactory.sol#L127
`for (uint256 i = 0; i < exCallData.length; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/factory/CashKYCSenderFactory.sol#L137
`for (uint256 i = 0; i < exCallData.length; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137
`for (uint256 i = 0; i < exCallData.length; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/kyc/KYCRegistry.sol#L163
`for (uint256 i = 0; i < length; i++) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/kyc/KYCRegistry.sol#L180
`for (uint256 i = 0; i < length; i++) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/CashManager.sol#L750
`for (uint256 i = 0; i < size; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/CashManager.sol#L786
`for (uint256 i = 0; i < size; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/CashManager.sol#L933
`for (uint256 i = 0; i < size; ++i) {`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/cash/CashManager.sol#L961
`for (uint256 i = 0; i < exCallData.length; ++i) {`

### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header

## Store using `Struct` over multiple `mappings`
### Summary
All these variables could be combine in a `Struct` in order to reduce the gas cost. 

### Details
As noticed in this [gist](https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2)

When multiple `mappings` that access the same `addresses`, `uints`, etc, all of them can be mixed into an `struct` and then that data accessed like:
`mapping(datatype => newStructCreated) newStructMap;`

Also, you have this [post](https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e) where it explains the benefits of using `Structs` over `mappings` 

### Github Permalinks

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/CashManager.sol#L78-L86
All indexes are `epoch`

```solidity
  // Mapping from epoch to redemption info struct for that epoch
  mapping(uint256 => RedemptionRequests) public redemptionInfoPerEpoch;

  // Mapping used for getting the exchange rate during a given epoch
  mapping(uint256 => uint256) public epochToExchangeRate;

  // Nested mapping containing mint requests for an epoch
  // { <epoch> : {<user> : <collateralAmount> }
  mapping(uint256 => mapping(address => uint256)) public mintRequestsPerEpoch;
```
- - - 

All indexes are `fToken`
https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/OndoPriceOracleV2.sol#L54-L62

```solidity
  /// @notice fToken to Oracle Type association
  mapping(address => OracleType) public fTokenToOracleType;

  /// @notice Contract storage for fToken's underlying asset prices
  mapping(address => uint256) public fTokenToUnderlyingPrice;

  /// @notice fToken to cToken associations for piggy backing off
  ///         of Compound's Oracle
  mapping(address => address) public fTokenToCToken;
```

### Mitigation
Consider mixing different mappings into a struct when able in order to save gas.


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the `abi.encode` function to `abi.encodePacked` can save gas since the `abi.encode` function pads extra null bytes at the end of the call data, which is unnecessary. 
Also, in general, `abi.encodePacked` is more gas-efficient.

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/kyc/KYCRegistry.sol#L94

### Mitigation
Consider changing `abi.encode` to `abi.encodePacked`

## State variables should be cached in stack variables rather than re-reading them from storage
`NOTE`: None of these findings where found by [Picodes. GAS-5](https://gist.github.com/iFrostizz/bbbadb3d93229f60c94f01b6626c056d)
### Summary
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

Saves `100` gas per instance

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/CashManager.sol#L221-L225

### Mitigation
Cache the variable

```diff
+ uint _currentEpoch = currentEpoch;
- mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
+ mintRequestsPerEpoch[_currentEpoch][msg.sender] += depositValueAfterFees;

emit MintRequested(
    msg.sender,
-    currentEpoch,
+    _currentEpoch,
```

## duplicated `require()` check should be refactored
### Summary
duplicated `require()` / `revert()` checks should be refactored to a modifier or function to save gas
### Details
Event appears twice and can be reduced

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cCash/CCash.sol#L220
`require(success, "TOKEN_TRANSFER_IN_FAILED");`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cCash/CCash.sol#L260
`require(success, "TOKEN_TRANSFER_OUT_FAILED");`

- - - -
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cToken/CTokenModified.sol#L681
`require(_getKYCStatus(borrower), "Borrower not KYC'd");`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cToken/CTokenModified.sol#L774
`require(_getKYCStatus(borrower), "Borrower not KYC'd");`

### Mitigation
refactor this checks to different functions to save gas


## Splitting `require()` statements that use `&&` saves gas
### Summary
Instead of using the `&&` operator in a single require statement to check multiple conditions, consider using multiple require statements with 1 condition per require statement (saving `3` gas per `&` ):

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/OndoPriceOracleV2.sol#L293
`(answeredInRound >= roundId) &&`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cCash/CTokenCash.sol#L46
`accrualBlockNumber == 0 && borrowIndex == 0,`

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70//contracts/lending/tokens/cToken/CTokenModified.sol#L46
`accrualBlockNumber == 0 && borrowIndex == 0,`

### Mitigation
Split require statements

