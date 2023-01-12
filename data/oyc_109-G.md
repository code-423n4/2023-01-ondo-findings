## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2023-01-ondo/contracts/cash/CashManager.sol::750 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::786 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::933 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::961 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashFactory.sol::127 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::85 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::135 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::167 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::113 => uint startingAllowance = 0;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::113 => uint startingAllowance = 0;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2023-01-ondo/contracts/cash/CashManager.sol::961 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashFactory.sol::127 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
```

## [G-03] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
2023-01-ondo/contracts/cash/factory/CashFactory.sol::152 => require(msg.sender == guardian, "CashFactory: You are not the Guardian");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::87 => require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::89 => require(msg.sender == owner, "only the owner may call this function.");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::44 => require(msg.sender == admin, "only admin may initialize the market");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::67 => require(err == NO_ERROR, "setting interest rate model failed");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1357 => require(msg.sender == admin, "Only admin can set KYC requirement group");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1389 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::44 => require(msg.sender == admin, "only admin may initialize the market");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::67 => require(err == NO_ERROR, "setting interest rate model failed");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1360 => require(msg.sender == admin, "Only admin can set KYC requirement group");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1392 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```

## [G-04] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::101 => // Seed/Series A Tranche Balance = proportionAvail*2/3 + x/3, where x = Balance.
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::14 => uint constant halfExpScale = expScale / 2;
```

## [G-05] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::29 => function truncate(Exp memory exp) internal pure returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::90 => function isZeroExp(Exp memory value) internal pure returns (bool) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::110 => function add_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::125 => function sub_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::140 => function mul_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::144 => function mul_(Exp memory a, uint b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::148 => function mul_(uint a, Exp memory b) internal pure returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::159 => function mul_(Double memory a, uint b) internal pure returns (Double memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::163 => function mul_(uint a, Double memory b) internal pure returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::171 => function div_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::175 => function div_(Exp memory a, uint b) internal pure returns (Exp memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::179 => function div_(uint a, Exp memory b) internal pure returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::190 => function div_(Double memory a, uint b) internal pure returns (Double memory) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::194 => function div_(uint a, Double memory b) internal pure returns (uint) {
```

## [G-06] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2023-01-ondo/contracts/cash/CashManager.sol::40 => IERC20 public immutable collateral;
2023-01-ondo/contracts/cash/CashManager.sol::43 => Cash public immutable cash;
2023-01-ondo/contracts/cash/CashManager.sol::93 => uint256 public immutable decimalsMultiplier;
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::48 => ISanctionsList public immutable sanctionsList;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::15 => uint256 public immutable tranche1VestingPeriod;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::17 => uint256 public immutable tranche2VestingPeriod;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::19 => uint256 public immutable seedVestingPeriod;
```

## [G-07] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2023-01-ondo/contracts/cash/CashManager.sol::526 => function pause() external onlyRole(PAUSER_ADMIN) {
2023-01-ondo/contracts/cash/CashManager.sol::533 => function unpause() external onlyRole(MANAGER_ADMIN) {
2023-01-ondo/contracts/cash/CashManager.sol::596 => function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::80 => function setPrice(address fToken, uint256 price) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::106 => function setOracle(address newOracle) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::163 => function setPrice(address fToken, uint256 price) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::182 => function setOracle(address newOracle) external override onlyOwner {
```

## [G-08] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::32 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::28 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::202 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::30 => uint8 public decimals;
```

## [G-09] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::39 => mapping(uint256 => mapping(address => bool)) public kycState;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::184 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::118 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol::6 => bool public constant isComptroller = true;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol::10 => bool public constant isInterestRateModel = true;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::368 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::182 => bool public constant isCToken = true;
```

## [G-10] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2023-01-ondo/contracts/cash/CashManager.sol::750 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::786 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::933 => for (uint256 i = 0; i < size; ++i) {
2023-01-ondo/contracts/cash/CashManager.sol::961 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashFactory.sol::127 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::85 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::135 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::167 => for (uint i = 0; i < cTokenCount; i++) {
```

## [G-11] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2023-01-ondo/contracts/cash/CashManager.sol::221 => mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
2023-01-ondo/contracts/cash/CashManager.sol::582 => currentEpoch += epochDifference;
2023-01-ondo/contracts/cash/CashManager.sol::630 => currentMintAmount += collateralAmountIn;
2023-01-ondo/contracts/cash/CashManager.sol::649 => currentRedeemAmount += amount;
2023-01-ondo/contracts/cash/CashManager.sol::680 => ] += amountCashToRedeem;
2023-01-ondo/contracts/cash/CashManager.sol::681 => redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
2023-01-ondo/contracts/cash/CashManager.sol::792 => totalCashAmountRefunded += cashAmountBurned;
2023-01-ondo/contracts/cash/CashManager.sol::865 => redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
2023-01-ondo/contracts/cash/CashManager.sol::867 => redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```

## [G-12] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::94 => abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```

## [G-13] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2023-01-ondo/contracts/cash/CashManager.sol::965 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::131 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::152 => require(msg.sender == guardian, "CashFactory: You are not the Guardian");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::141 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::141 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::87 => require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::92 => require(block.timestamp <= deadline, "KYCRegistry: signature expired");
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::89 => require(msg.sender == owner, "only the owner may call this function.");
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::297 => require(answer >= 0, "Price cannot be negative");
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::220 => require(success, "TOKEN_TRANSFER_IN_FAILED");
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::260 => require(success, "TOKEN_TRANSFER_OUT_FAILED");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::44 => require(msg.sender == admin, "only admin may initialize the market");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::59 => require(err == NO_ERROR, "setting comptroller failed");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::67 => require(err == NO_ERROR, "setting interest rate model failed");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::97 => require(_getKYCStatus(spender), "Spender not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::98 => require(_getKYCStatus(src), "Source not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::99 => require(_getKYCStatus(dst), "Destination not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::493 => require(_getKYCStatus(minter), "Minter not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::582 => require(_getKYCStatus(redeemer), "Redeemer not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::681 => require(_getKYCStatus(borrower), "Borrower not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::773 => require(_getKYCStatus(payer), "Payer not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::774 => require(_getKYCStatus(borrower), "Borrower not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::997 => require(_getKYCStatus(liquidator), "Liquidator not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::998 => require(_getKYCStatus(borrower), "Borrower not KYC'd");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1122 => require(newComptroller.isComptroller(), "marker method returned false");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1357 => require(msg.sender == admin, "Only admin can set KYC requirement group");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1379 => require(msg.sender == admin, "Only admin can set KYC registry");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1389 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1435 => require(_notEntered, "re-entered");
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::220 => require(success, "TOKEN_TRANSFER_IN_FAILED");
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::260 => require(success, "TOKEN_TRANSFER_OUT_FAILED");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::44 => require(msg.sender == admin, "only admin may initialize the market");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::59 => require(err == NO_ERROR, "setting comptroller failed");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::67 => require(err == NO_ERROR, "setting interest rate model failed");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::97 => require(!sanctionsList.isSanctioned(spender), "Spender is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::98 => require(!sanctionsList.isSanctioned(src), "Source is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::99 => require(!sanctionsList.isSanctioned(dst), "Destination is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::493 => require(!sanctionsList.isSanctioned(minter), "Minter is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::582 => require(!sanctionsList.isSanctioned(redeemer), "Redeemer is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::681 => require(_getKYCStatus(borrower), "Borrower not KYC'd");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::773 => require(_getKYCStatus(payer), "Payer not KYC'd");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::774 => require(_getKYCStatus(borrower), "Borrower not KYC'd");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1001 => require(!sanctionsList.isSanctioned(borrower), "Borrower is sanctioned");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1125 => require(newComptroller.isComptroller(), "marker method returned false");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1360 => require(msg.sender == admin, "Only admin can set KYC requirement group");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1382 => require(msg.sender == admin, "Only admin can set KYC registry");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1392 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1438 => require(_notEntered, "re-entered");
```

## [G-14] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::14 => uint constant halfExpScale = expScale / 2;
```

## [G-15] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/ondo/ondo-token/IOndo.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
```

## [G-16] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::85 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::135 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::167 => for (uint i = 0; i < cTokenCount; i++) {
```

## [G-17] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

```
2023-01-ondo/contracts/cash/factory/CashFactory.sol::97 => assert(cashProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::106 => assert(cashKYCSenderProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::106 => assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```

## [G-18] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::275 => * @dev This function is public for observability purposes only.
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::21 => function _becomeImplementation(bytes memory data) public virtual override {
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::39 => function _resignImplementation() public virtual override {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::21 => function _becomeImplementation(bytes memory data) public virtual override {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::39 => function _resignImplementation() public virtual override {
```

## [G-19] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2023-01-ondo/contracts/cash/CashManager.sol::784 => ) private returns (uint256 totalCashAmountRefunded) {
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::52 => ) external view returns (uint256, uint256) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1198 => function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::956 => ) external view returns (uint256, uint256, uint256, uint256) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1201 => function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
```

## [G-20] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2023-01-ondo/contracts/cash/CashManager.sol::75 => mapping(address => uint256) addressToBurnAmt;
2023-01-ondo/contracts/cash/CashManager.sol::86 => mapping(uint256 => mapping(address => uint256)) public mintRequestsPerEpoch;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::45 => mapping(address => uint256) public fTokenToUnderlyingPrice;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::49 => mapping(address => address) public fTokenToCToken;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::55 => mapping(address => OracleType) public fTokenToOracleType;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::58 => mapping(address => uint256) public fTokenToUnderlyingPrice;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::62 => mapping(address => address) public fTokenToCToken;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::70 => mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::73 => mapping(address => uint256) public fTokenToUnderlyingPriceCap;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::94 => mapping(address => uint) internal accountTokens;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::97 => mapping(address => mapping(address => uint)) internal transferAllowances;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::110 => mapping(address => BorrowSnapshot) internal accountBorrows;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::90 => mapping(address => uint) internal accountTokens;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::93 => mapping(address => mapping(address => uint)) internal transferAllowances;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::106 => mapping(address => BorrowSnapshot) internal accountBorrows;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::273 => mapping(address => uint256) internal accountTokens;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::278 => mapping(address => mapping(address => uint256)) internal transferAllowances;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::293 => mapping(address => BorrowSnapshot) internal accountBorrows;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::92 => mapping(address => uint) internal accountTokens;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::95 => mapping(address => mapping(address => uint)) internal transferAllowances;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::108 => mapping(address => BorrowSnapshot) internal accountBorrows;
```

## [G-21] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::39 => function _setKYCRegistry(address _kycRegistry) internal {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::54 => function _setKYCRequirementGroup(uint256 _kycRequirementGroup) internal {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::65 => function _getKYCStatus(address account) internal view returns (bool) {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::119 => function _setFTokenToCToken(address fToken, address cToken) internal {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::210 => function _setFTokenToCToken(address fToken, address cToken) internal {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::324 => function _min(uint256 a, uint256 b) internal pure returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::479 => function mintInternal(uint mintAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::491 => function mintFresh(address minter, uint mintAmount) internal {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::552 => function redeemInternal(uint redeemTokens) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::563 => function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::669 => function borrowInternal(uint borrowAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::679 => function borrowFresh(address payable borrower, uint borrowAmount) internal {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::740 => function repayBorrowInternal(uint repayAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1198 => function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1253 => function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::58 => function fail(Error err, FailureInfo info) internal returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::90 => function isZeroExp(Exp memory value) internal pure returns (bool) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::202 => function fraction(uint a, uint b) internal pure returns (Double memory) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::479 => function mintInternal(uint mintAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::491 => function mintFresh(address minter, uint mintAmount) internal {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::552 => function redeemInternal(uint redeemTokens) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::563 => function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::669 => function borrowInternal(uint borrowAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::679 => function borrowFresh(address payable borrower, uint borrowAmount) internal {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::740 => function repayBorrowInternal(uint repayAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1201 => function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1256 => function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
```

## [G-22] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::39 => function _setKYCRegistry(address _kycRegistry) internal {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::54 => function _setKYCRequirementGroup(uint256 _kycRequirementGroup) internal {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::65 => function _getKYCStatus(address account) internal view returns (bool) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::479 => function mintInternal(uint mintAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::552 => function redeemInternal(uint redeemTokens) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::563 => function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::669 => function borrowInternal(uint borrowAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::740 => function repayBorrowInternal(uint repayAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::58 => function fail(Error err, FailureInfo info) internal returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::90 => function isZeroExp(Exp memory value) internal pure returns (bool) {
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::202 => function fraction(uint a, uint b) internal pure returns (Double memory) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::479 => function mintInternal(uint mintAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::552 => function redeemInternal(uint redeemTokens) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::563 => function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::669 => function borrowInternal(uint borrowAmount) internal nonReentrant {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::740 => function repayBorrowInternal(uint repayAmount) internal nonReentrant {
```