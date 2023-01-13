

### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```

2023-01-ondo/contracts/cash/CashManager.sol::47 => address public assetRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::50 => address public feeRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::53 => address public assetSender;
2023-01-ondo/contracts/cash/CashManager.sol::97 => uint256 public currentEpoch;
2023-01-ondo/contracts/cash/CashManager.sol::100 => uint256 public epochDuration;
2023-01-ondo/contracts/cash/CashManager.sol::103 => uint256 public currentEpochStartTimestamp;
2023-01-ondo/contracts/cash/CashManager.sol::110 => uint256 public mintLimit;
2023-01-ondo/contracts/cash/CashManager.sol::113 => uint256 public currentMintAmount;
2023-01-ondo/contracts/cash/CashManager.sol::116 => uint256 public redeemLimit;
2023-01-ondo/contracts/cash/CashManager.sol::119 => uint256 public currentRedeemAmount;
2023-01-ondo/contracts/cash/factory/CashFactory.sol::49 => Cash public cashImplementation;
2023-01-ondo/contracts/cash/factory/CashFactory.sol::50 => ProxyAdmin public cashProxyAdmin;
2023-01-ondo/contracts/cash/factory/CashFactory.sol::51 => TokenProxy public cashProxy;
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::49 => CashKYCSender public cashKYCSenderImplementation;
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::50 => ProxyAdmin public cashKYCSenderProxyAdmin;
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::51 => TokenProxy public cashKYCSenderProxy;
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::49 => CashKYCSenderReceiver public cashKYCSenderReceiverImplementation;
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::50 => ProxyAdmin public cashKYCSenderReceiverProxyAdmin;
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::51 => TokenProxy public cashKYCSenderReceiverProxy;
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::30 => IKYCRegistry public override kycRegistry;
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::32 => uint256 public override kycRequirementGroup;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::24 => address public owner;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::34 => uint public multiplierPerBlock;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::39 => uint public baseRatePerBlock;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::44 => uint public jumpMultiplierPerBlock;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::49 => uint public kink;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::22 => string public name;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::27 => string public symbol;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::32 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::43 => address payable public admin;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::48 => address payable public pendingAdmin;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::53 => ComptrollerInterface public comptroller;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::58 => InterestRateModel public interestRateModel;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::66 => uint public reserveFactorMantissa;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::71 => uint public accrualBlockNumber;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::76 => uint public borrowIndex;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::81 => uint public totalBorrows;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::86 => uint public totalReserves;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::91 => uint public totalSupply;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::172 => IKYCRegistry public kycRegistry;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::177 => uint256 public kycRequirementGroup;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::387 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::425 => address public implementation;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::192 => string public name;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::197 => string public symbol;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::202 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::218 => address payable public admin;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::223 => address payable public pendingAdmin;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::228 => ComptrollerInterface public comptroller;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::233 => InterestRateModel public interestRateModel;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::243 => uint256 public reserveFactorMantissa;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::248 => uint256 public accrualBlockNumber;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::253 => uint256 public borrowIndex;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::258 => uint256 public totalBorrows;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::263 => uint256 public totalReserves;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::268 => uint256 public totalSupply;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::356 => IKYCRegistry public kycRegistry;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::361 => uint256 public kycRequirementGroup;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::573 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::609 => address public implementation;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::20 => string public name;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::25 => string public symbol;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::30 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::41 => address payable public admin;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::46 => address payable public pendingAdmin;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::51 => ComptrollerInterface public comptroller;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::56 => InterestRateModel public interestRateModel;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::64 => uint public reserveFactorMantissa;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::69 => uint public accrualBlockNumber;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::74 => uint public borrowIndex;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::79 => uint public totalBorrows;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::84 => uint public totalReserves;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::89 => uint public totalSupply;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::170 => IKYCRegistry public kycRegistry;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::175 => uint256 public kycRequirementGroup;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::385 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::423 => address public implementation;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas).
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::47 => address public assetRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::50 => address public feeRecipient;
2023-01-ondo/contracts/cash/CashManager.sol::53 => address public assetSender;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::24 => address public owner;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::387 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::425 => address public implementation;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::573 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::609 => address public implementation;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::385 => address public underlying;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::423 => address public implementation;
```


### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::961 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashFactory.sol::127 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::137 => for (uint256 i = 0; i < exCallData.length; ++i) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops



#### Findings:
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
```



### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas



#### Findings:
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




### [G06] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
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
```



### [G07] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)



#### Findings:
```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
```


### [G08] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::83 => uint8 v,
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::35 => function decimals() external view returns (uint8);
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::286 => uint80 roundId,
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::290 => uint80 answeredInRound
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::37 => uint8 decimals_,
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::40 => uint8 decimals_,
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::32 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::202 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::37 => uint8 decimals_,
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::30 => uint8 public decimals;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::40 => uint8 decimals_,
```



### [G09] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detailed description of the issue
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



### [G10] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function



#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::346 => revert CannotServiceFutureEpoch();
2023-01-ondo/contracts/cash/CashManager.sol::372 => revert MustServicePastEpoch();
```



### [G11] `require()` or `revert()` statements that check input arguments should be at the top of the function



#### Findings:
```
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1389 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::1392 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
```



### [G12] Use custom errors rather than `revert()`/`require()` strings to save deployment gas



#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::965 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::131 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::152 => require(msg.sender == guardian, "CashFactory: You are not the Guardian");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::141 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::141 => require(success, "Call Failed");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::87 => require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::92 => require(block.timestamp <= deadline, "KYCRegistry: signature expired");
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::89 => require(msg.sender == owner, "only the owner may call this function.");
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::110 => revert("Oracle type not supported");
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::297 => require(answer >= 0, "Price cannot be negative");
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::220 => require(success, "TOKEN_TRANSFER_IN_FAILED");
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::260 => require(success, "TOKEN_TRANSFER_OUT_FAILED");
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
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1362 => * @notice Sets the KYC registry requirement group for this
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1379 => require(msg.sender == admin, "Only admin can set KYC registry");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1389 => require(_kycRegistry != address(0), "KYC registry cannot be zero address");
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::1435 => require(_notEntered, "re-entered");
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::220 => require(success, "TOKEN_TRANSFER_IN_FAILED");
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::260 => require(success, "TOKEN_TRANSFER_OUT_FAILED");
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



### [G13] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::526 => function pause() external onlyRole(PAUSER_ADMIN) {
2023-01-ondo/contracts/cash/CashManager.sol::533 => function unpause() external onlyRole(MANAGER_ADMIN) {
2023-01-ondo/contracts/cash/CashManager.sol::596 => function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::80 => function setPrice(address fToken, uint256 price) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::106 => function setOracle(address newOracle) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::163 => function setPrice(address fToken, uint256 price) external override onlyOwner {
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::182 => function setOracle(address newOracle) external override onlyOwner {
```




### [G14] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.
#### Findings:
```
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::160 => token.transfer(admin, balance);
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::201 => token.transferFrom(from, address(this), amount);
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::241 => token.transfer(to, amount);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::160 => token.transfer(admin, balance);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::201 => token.transferFrom(from, address(this), amount);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::241 => token.transfer(to, amount);
```

### [G15] Don’t use SafeMath once the solidity version is 0.8.0 or greater

#### Impact
Version 0.8.0 introduces internal overflow checks, so using SafeMath is redundant and adds overhead
#### Findings:
```
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::116 => return borrows.mul(1e18).div(cash.add(borrows).sub(reserves));
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::134 => return util.mul(multiplierPerBlock).div(1e18).add(baseRatePerBlock);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::136 => uint normalRate = kink.mul(multiplierPerBlock).div(1e18).add(
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::139 => uint excessUtil = util.sub(kink);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::140 => return excessUtil.mul(jumpMultiplierPerBlock).div(1e18).add(normalRate);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::158 => uint oneMinusReserveFactor = uint(1e18).sub(reserveFactorMantissa);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::160 => uint rateToPool = borrowRate.mul(oneMinusReserveFactor).div(1e18);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::161 => return utilizationRate(cash, borrows, reserves).mul(rateToPool).div(1e18);
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::178 => multiplierPerBlock = (multiplierPerYear.mul(1e18)).div(
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::179 => blocksPerYear.mul(kink_)
```



### [G16] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::55 => mapping(address => OracleType) public fTokenToOracleType;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::58 => mapping(address => uint256) public fTokenToUnderlyingPrice;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::70 => mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::73 => mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```


### [G17] Using `bools` for storage incurs overhead


#### Findings:
```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::39 => mapping(uint256 => mapping(address => bool)) public kycState;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::17 => bool internal _notEntered;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::184 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::187 => bool internal _notEntered;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::368 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::15 => bool internal _notEntered;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::182 => bool public constant isCToken = true;
```


### [G18] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. 
#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::89 => uint256 public constant BPS_DENOMINATOR = 10_000;
2023-01-ondo/contracts/cash/CashManager.sol::122 => bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::123 => bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::124 => bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::46 => bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::46 => bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::46 => bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::31 => bytes32 public constant _APPROVAL_TYPEHASH =
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::36 => bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
2023-01-ondo/contracts/cash/token/Cash.sol::22 => bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::26 => bytes32 public constant KYC_CONFIGURER_ROLE =
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::26 => bytes32 public constant KYC_CONFIGURER_ROLE =
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::29 => uint public constant blocksPerYear = 2628000;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::115 => uint public constant protocolSeizeShareMantissa = 0; //0%
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::166 => ISanctionsList public constant sanctionsList =
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::184 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::350 => ISanctionsList public constant sanctionsList =
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::368 => bool public constant isCToken = true;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::113 => uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::164 => ISanctionsList public constant sanctionsList =
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::182 => bool public constant isCToken = true;
```



### [G19] Usage of `assert()` instead of `require()`


#### Findings:
```
2023-01-ondo/contracts/cash/factory/CashFactory.sol::97 => assert(cashProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::106 => assert(cashKYCSenderProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::106 => assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```




### [G20] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2023-01-ondo/contracts/cash/token/Proxy.sol::25 => ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::15 => constructor() {}
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::15 => constructor() {}
```




### [G21] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::94 => abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```



### [G22] Optimize names to save gas

#### Impact
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
2023-01-ondo/contracts/cash/kyc/KYCRegistryClient.sol::28 => abstract contract KYCRegistryClient is IKYCRegistryClient {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClientConstructable.sol::29 => abstract contract KYCRegistryClientConstructable is KYCRegistryClient {
2023-01-ondo/contracts/cash/kyc/KYCRegistryClientInitializable.sol::30 => abstract contract KYCRegistryClientInitializable is
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::17 => abstract contract CTokenCash is
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::180 => abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::390 => abstract contract CErc20Interface is CErc20Storage {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::428 => abstract contract CDelegatorInterface is CDelegationStorage {
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::447 => abstract contract CDelegateInterface is CDelegationStorage {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::178 => abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::388 => abstract contract CErc20Interface is CErc20Storage {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::426 => abstract contract CDelegatorInterface is CDelegationStorage {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::445 => abstract contract CDelegateInterface is CDelegationStorage {
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::17 => abstract contract CTokenModified is
```



