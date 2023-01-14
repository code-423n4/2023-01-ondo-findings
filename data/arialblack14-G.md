# GAS OPTIMIZATION REPORT ⛽

## [G-1] Use shift right/left instead of division/multiplication if possible.

### Description
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
urthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

### ✅ Recommendation
You should change multiplication and division with powers of two to bit shift.

#### *Instances(1):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L491 )
```solidity
491: uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;
```

## [G-2] `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when  it is not possible for them to overflow, for example when used in `for` and `while` loops

### Description
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695). 
			Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
// do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
	// do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
	unchecked {
		return i + 1;
	}
}
```
Or just:

```Solidity
for (uint i = 0; i < length;) {
	// do something that doesn't change the value of i
	unchecked { i++; 	}
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)


### ✅ Recommendation
Use the `unchecked` keyword

#### *Instances(9):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L750 )
```solidity
750: for (uint256 i = 0; i < size; ++i) {
786: for (uint256 i = 0; i < size; ++i) {
933: for (uint256 i = 0; i < size; ++i) {
961: for (uint256 i = 0; i < exCallData.length; ++i) {
```
File: [2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L127 )
```solidity
127: for (uint256 i = 0; i < exCallData.length; ++i) {
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137 )
```solidity
137: for (uint256 i = 0; i < exCallData.length; ++i) {
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137 )
```solidity
137: for (uint256 i = 0; i < exCallData.length; ++i) {
```
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L163 )
```solidity
163: for (uint256 i = 0; i < length; i++) {
180: for (uint256 i = 0; i < length; i++) {
```

## [G-3] Use assembly to check for address(0)

### Description
You can save about 6 gas per instance if using assembly to check for address(0)

### ✅ Recommendation


#### *Instances(13):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L141 )
```solidity
141: if (_collateral == address(0)) {
144: if (_cash == address(0)) {
147: if (_assetRecipient == address(0)) {
150: if (_assetSender == address(0)) {
153: if (_feeRecipient == address(0)) {
```
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClient.sol#L40 )
```solidity
40: if (_kycRegistry == address(0)) {
```
File: [2023-01-ondo/contracts/cash/token/CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L68 )
```solidity
68: if (from != address(0)) {
```
File: [2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L68 )
```solidity
68: if (from != address(0)) {
76: if (to != address(0)) {
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1087 )
```solidity
1087: if (msg.sender != pendingAdmin || msg.sender == address(0)) {
1389: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1090 )
```solidity
1090: if (msg.sender != pendingAdmin || msg.sender == address(0)) {
1392: require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```

## [G-4] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate.

### Description
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### ✅ Recommendation
Where appropriate, you can combine multiple address mappings into a single mapping of an address to a struct.

#### *Instances(4):*
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L97 )
```solidity
97: mapping(address => mapping(address => uint)) internal transferAllowances;
```
File: [2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L278 )
```solidity
278: mapping(address => mapping(address => uint256)) internal transferAllowances;
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L95 )
```solidity
95: mapping(address => mapping(address => uint)) internal transferAllowances;
```
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L39 )
```solidity
39: mapping(uint256 => mapping(address => bool)) public kycState;
```

## [G-5] Upgrade `pragma` to latest solidity compiler version.

### Description
Using newer compiler versions and the optimizer gives gas optimizations and additional safety checks for free!
Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value 
The advantages of versions `0.8.*` over `<0.8.0` are:

> Safemath by default from `0.8.0` (can be more gas efficient than some library based safemath).

> [Low level inliner](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) from `0.8.2`, leads to cheaper runtime gas. Especially relevant when the contract has small functions. For example, OpenZeppelin libraries typically have a lot of small helper functions and if they are not inlined, they cost an additional 20 to 40 gas because of 2 extra jump instructions and additional stack operations needed for function calls.

> [Optimizer improvements in packed structs](https://blog.soliditylang.org/2021/03/23/solidity-0.8.3-release-announcement/#optimizer-improvements): Before `0.8.3`, storing packed structs, in some cases used an additional storage read operation. After [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929), if the slot was already cold, this means unnecessary stack operations and extra deploy time costs. However, if the slot was already warm, this means additional cost of 100 gas alongside the same unnecessary stack operations and extra deploy time costs.

> [Custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors) from `0.8.4`, leads to cheaper deploy time cost and run time cost. Note: the run time cost is only relevant when the revert condition is met. In short, replace revert strings by custom errors.

> Solidity `0.8.10` has a useful change which [reduced gas costs of external calls](https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/) which expect a return value.  Code Generator skips existence check for external contract if return data is expected. In this case, the ABI decoder will revert if the contract does not exist. `0.8.10` also enables the new EVM code generator for pure Yul mode.

> [Improved Inlining Heuristics in Yul Optimizer](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/). The compiler used to be very conservative before Solidity version `0.8.15` in deciding whether to inline a function or not. This was necessary due to the fact that inlining may easily increase stack pressure and lead to the dreaded `Stack too deep` error. In `0.8.15` the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

> [Overflow checks on multiplication more efficient](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/) in Solidity v0.8.17. Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call;  Simplify the starting offset of zero-length operations to zero.  Code Generator: More efficient overflow checks for multiplication.

### ✅ Recommendation
Consider whether you should choose the latest version.
The latest version has its advantages, but also it has disadvantages, such as the possibility of unknown bugs.

#### *Instances(10):*
File: [2023-01-ondo/contracts/lending/IOndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracle.sol#L15 )
```solidity
15: pragma solidity 0.6.12;
```
File: [2023-01-ondo/contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L1 )
```solidity
1: pragma solidity ^0.5.16;
```
File: [2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L15 )
```solidity
15: pragma solidity 0.6.12;
```
File: [2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7 )
```solidity
7: pragma solidity ^0.5.12;
138: pragma solidity ^0.5.12;
181: pragma solidity ^0.5.12;
296: pragma solidity ^0.5.16;
302: pragma solidity ^0.5.16;
324: pragma solidity ^0.5.16;
647: pragma solidity ^0.5.12;
```

## [G-6] Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead.

### Description
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### ✅ Recommendation
Use `uint256` instead.

#### *Instances(13):*
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L83 )
```solidity
83: uint8 v,
```
File: [2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L35 )
```solidity
35: function decimals() external view returns (uint8);
286: uint80 roundId,
290: uint80 answeredInRound
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L37 )
```solidity
37: uint8 decimals_,
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L40 )
```solidity
40: uint8 decimals_,
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L32 )
```solidity
32: uint8 public decimals;
```
File: [2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L202 )
```solidity
202: uint8 public decimals;
680: uint8 decimals_,
694: "initialize(address,address,address,uint256,string,string,uint8,address,uint256)",
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L37 )
```solidity
37: uint8 decimals_,
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L30 )
```solidity
30: uint8 public decimals;
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L40 )
```solidity
40: uint8 decimals_,
```

## [G-7] `abi.encode()` is less efficient than `abi.encodePacked()`

### Description
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))` 
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

### ✅ Recommendation
Use `abi.encodePacked()` where possible to save gas

#### *Instances(1):*
File: [2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L94 )
```solidity
94: abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```

## [G-8] Internal functions only called once can be inlined to save gas.

### Description
Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
Check this out: [link](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2)
Note: To sum up, when there is an internal function that is only called once, there is no point for that function to exist. 

### ✅ Recommendation
Remove the function and make it inline if it is a simple function.

#### *Instances(2):*
File: [2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L119 )
```solidity
119: function _setFTokenToCToken(address fToken, address cToken) internal {
```
File: [2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L210 )
```solidity
210: function _setFTokenToCToken(address fToken, address cToken) internal {
```

## [G-9] Multiple accesses of a mapping/array should use a local variable cache.

### Description
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata. [Similar findings](https://github.com/code-423n4/2022-06-infinity-findings/issues/186)

### ✅ Recommendation
Use a local variable cache.

#### *Instances(12):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L962 )
```solidity
962: (bool success, bytes memory ret) = address(exCallData[i].target).call{
963: value: exCallData[i].value
964: }(exCallData[i].data);
```
File: [2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L128 )
```solidity
128: (bool success, bytes memory ret) = address(exCallData[i].target).call{
129: value: exCallData[i].value
130: }(exCallData[i].data);
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L138 )
```solidity
138: (bool success, bytes memory ret) = address(exCallData[i].target).call{
139: value: exCallData[i].value
140: }(exCallData[i].data);
```
File: [2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L138 )
```solidity
138: (bool success, bytes memory ret) = address(exCallData[i].target).call{
139: value: exCallData[i].value
140: }(exCallData[i].data);
```

## [G-10] `>=` costs less gas than `>`.

### Description
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, which [saves 3 gas](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

### ✅ Recommendation
Use `>=` instead if appropriate.

#### *Instances(17):*
File: [2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L296 )
```solidity
296: if (exchangeRate > lastSetMintExchangeRate) {
305: if (rateDifference > maxDifferenceThisEpoch) {
345: if (epoch > currentEpoch) {
579: if (epochDifference > 0) {
626: if (collateralAmountIn > mintLimit - currentMintAmount) {
645: if (amount > redeemLimit - currentRedeemAmount) {
856: if (epoch > currentEpoch) {
866: } else if (balance > previousBalance) {

File: [2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L114 )
```solidity
114: if (fTokenToUnderlyingPriceCap[fToken] > 0) {
```
File: [2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L53 )
```solidity
53: initialExchangeRateMantissa > 0,
595: if (redeemTokensIn > 0) {
1165: if (newReserveFactorMantissa > reserveFactorMaxMantissa) {
1273: if (reduceAmount > totalReserves) {
```
File: [2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L53 )
```solidity
53: initialExchangeRateMantissa > 0,
595: if (redeemTokensIn > 0) {
1168: if (newReserveFactorMantissa > reserveFactorMaxMantissa) {
1276: if (reduceAmount > totalReserves) {
```