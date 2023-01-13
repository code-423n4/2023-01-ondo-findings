
### [L01] require() should be used instead of assert()


#### Findings:
```
2023-01-ondo/contracts/cash/factory/CashFactory.sol::97 => assert(cashProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::106 => assert(cashKYCSenderProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::106 => assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```






### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::165 => feeRecipient = _feeRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::166 => assetRecipient = _assetRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::167 => assetSender = _assetSender;
2023-01-ondo/contracts/cash/CashManager.sol::168 => currentEpoch = currentEpoch;
2023-01-ondo/contracts/cash/CashManager.sol::170 => mintLimit = _mintLimit;
2023-01-ondo/contracts/cash/CashManager.sol::171 => redeemLimit = _redeemLimit;
2023-01-ondo/contracts/cash/CashManager.sol::172 => epochDuration = _epochDuration;
2023-01-ondo/contracts/cash/CashManager.sol::377 => lastSetMintExchangeRate = _lastSetMintExchangeRate;
2023-01-ondo/contracts/cash/CashManager.sol::395 => uint256 oldExchangeRateDeltaLimit = exchangeRateDeltaLimit;
2023-01-ondo/contracts/cash/CashManager.sol::396 => exchangeRateDeltaLimit = _exchangeRateDeltaLimit;
2023-01-ondo/contracts/cash/CashManager.sol::417 => mintFee = _mintFee;
2023-01-ondo/contracts/cash/CashManager.sol::440 => minimumDepositAmount = _minimumDepositAmount;
2023-01-ondo/contracts/cash/CashManager.sol::456 => feeRecipient = _feeRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::469 => assetRecipient = _assetRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::491 => uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;
2023-01-ondo/contracts/cash/CashManager.sol::550 => epochDuration = _epochDuration;
2023-01-ondo/contracts/cash/CashManager.sol::598 => mintLimit = _mintLimit;
2023-01-ondo/contracts/cash/CashManager.sol::613 => redeemLimit = _redeemLimit;
2023-01-ondo/contracts/cash/factory/CashFactory.sol::54 => guardian = _guardian;
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::54 => guardian = _guardian;
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::54 => guardian = _guardian;
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::56 => kycRequirementGroup = _kycRequirementGroup;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::313 => maxChainlinkOracleTimeDelay = _maxChainlinkOracleTimeDelay;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1369 => kycRequirementGroup = _kycRequirementGroup;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1372 => kycRequirementGroup = _kycRequirementGroup;
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::46 => function initialize(
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::51 => ) public initializer {
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::46 => function initialize(
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::51 => ) public initializer {
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::30 => function initialize(
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::34 => function initialize(
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::30 => function initialize(
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::42 => super.initialize(
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::34 => function initialize(
```




### [L04] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
#### Findings:
```
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::2 => pragma solidity ^0.8.10;
```




### Non-Critical Issues


### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::118 => return price;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::377 => return exchangeRate;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::835 => return actualRepayAmount;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::283 => return totalBorrows;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings



#### Findings:
```
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::217 => revert(0, 0)
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::257 => revert(0, 0)
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1207 => revert(add(returnData, 0x20), returndatasize)
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1240 => revert(add(returnData, 0x20), returndatasize)
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1265 => revert(free_mem_ptr, returndatasize)
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::217 => revert(0, 0)
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::257 => revert(0, 0)
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::491 => uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::158 => uint oneMinusReserveFactor = uint(1e18).sub(reserveFactorMantissa);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::160 => uint rateToPool = borrowRate.mul(oneMinusReserveFactor).div(1e18);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::161 => return utilizationRate(cash, borrows, reserves).mul(rateToPool).div(1e18);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::178 => multiplierPerBlock = (multiplierPerYear.mul(1e18)).div(
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::260 => fTokenToChainlinkOracle[fToken].scaleFactor = (10 **
```



### [N04] Use a more recent version of solidity


#### Findings:
```
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
```




### [N05] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::76 => event FeeRecipientSet(address oldFeeRecipient, address newFeeRecipient);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::84 => event AssetRecipientSet(address oldAssetRecipient, address newAssetRecipient);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::92 => event AssetSenderSet(address oldAssetSender, address newAssetSender);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::102 => event MinimumDepositAmountSet(uint256 oldMinimum, uint256 newMinimum);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::111 => event MinimumRedeemAmountSet(uint256 oldRedeemMin, uint256 newRedeemMin);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::121 => event MintFeeSet(uint256 oldFee, uint256 newFee);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::164 => event ExchangeRateDeltaLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::189 => event MintLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::199 => event RedeemLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::209 => event EpochDurationSet(uint256 oldDuration, uint256 newDuration);
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::48 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::66 => event CTokenOracleSet(address oldOracle, address newOracle);
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::66 => event CTokenOracleSet(address oldOracle, address newOracle);
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::137 => event FTokenToOracleTypeSet(address indexed fToken, OracleType oracleType);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::150 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::201 => event Mint(address minter, uint mintAmount, uint mintTokens);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::206 => event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::245 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::250 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::297 => event Transfer(address indexed from, address indexed to, uint amount);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::302 => event Approval(address indexed owner, address indexed spender, uint amount);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::432 => event NewImplementation(address oldImplementation, address newImplementation);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::334 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::385 => event Mint(address minter, uint256 mintAmount, uint256 mintTokens);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::390 => event Redeem(address redeemer, uint256 redeemAmount, uint256 redeemTokens);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::429 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::434 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::481 => event Transfer(address indexed from, address indexed to, uint256 amount);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::495 => event Failure(uint256 error, uint256 info, uint256 detail);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::616 => event NewImplementation(address oldImplementation, address newImplementation);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::148 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::199 => event Mint(address minter, uint mintAmount, uint mintTokens);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::204 => event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::243 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::248 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::295 => event Transfer(address indexed from, address indexed to, uint amount);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::300 => event Approval(address indexed owner, address indexed spender, uint amount);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::430 => event NewImplementation(address oldImplementation, address newImplementation);
```




### [N06] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::576 => function transitionEpoch() public {
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::51 => ) public initializer {
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::51 => ) public initializer {
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::24 => address public owner;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::65 => ) public {
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::110 => ) public pure returns (uint) {
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::130 => ) public view returns (uint) {
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::157 => ) public view returns (uint) {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::279 => ) public view returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::40 => ) public {
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::21 => function _becomeImplementation(bytes memory data) public virtual override {
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::39 => function _resignImplementation() public virtual override {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::43 => ) public {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::305 => ) public view override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::338 => function exchangeRateCurrent() public override nonReentrant returns (uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::348 => function exchangeRateStored() public view override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::394 => function accrueInterest() public virtual override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1114 => ) public override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1302 => ) public override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::530 => function borrowBalanceStored(address account) public view returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::532 => function exchangeRateCurrent() public returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::534 => function exchangeRateStored() public view returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::538 => function accrueInterest() public returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::556 => ) public returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::566 => ) public returns (uint256);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::637 => function _becomeImplementation(bytes memory data) public;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::642 => function _resignImplementation() public;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::686 => ) public {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::724 => ) public {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1013 => function borrowBalanceStored(address account) public view returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1024 => function exchangeRateCurrent() public returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1036 => function exchangeRateStored() public view returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1059 => function accrueInterest() public returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1115 => ) public returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1183 => ) public returns (uint256) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1221 => ) public returns (bytes memory) {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1234 => ) public view returns (bytes memory) {
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::40 => ) public {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::21 => function _becomeImplementation(bytes memory data) public virtual override {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::39 => function _resignImplementation() public virtual override {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::43 => ) public {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::305 => ) public view override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::338 => function exchangeRateCurrent() public override nonReentrant returns (uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::348 => function exchangeRateStored() public view override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::394 => function accrueInterest() public virtual override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1117 => ) public override returns (uint) {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1305 => ) public override returns (uint) {
```



### [N07] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::99 => // Duration of an epoch in seconds
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::77 => uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
```



### [N08] Constant redefined elsewhere

#### Impact
Consider defining in only one contract so that values cannot become out of sync when only one location is updated
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::122 => bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::123 => bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::124 => bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::36 => bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
2023-01-ondo/contracts/cash/token/Cash.sol::22 => bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
```



### [N09] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::15 => pragma solidity 0.8.16;
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::2 => pragma solidity ^0.8.10;
```




### [N10] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern
#### Findings:
```
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::18 => interface PriceOracle {
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::27 => interface IOndoPriceOracle is PriceOracle {
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::18 => interface PriceOracle {
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::27 => interface IOndoPriceOracle is PriceOracle {
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::69 => interface IOndoPriceOracleV2 is IOndoPriceOracle {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::21 => interface CTokenOracle {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::27 => interface CTokenLike {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::22 => interface CTokenOracle {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::28 => interface CTokenLike {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::34 => interface IERC20Like {
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::6 => interface CompLike {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::118 => interface ISanctionsList {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::127 => interface IKYCRegistry {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::9 => interface ComptrollerInterface {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::144 => interface InterestRateModel {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::298 => interface ISanctionsList {
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::309 => interface IKYCRegistry {
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::6 => interface CompLike {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::116 => interface ISanctionsList {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::125 => interface IKYCRegistry {
```




