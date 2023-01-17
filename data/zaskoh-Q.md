## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| L-01 | Check for a max percent for fees in completeRedemptions | 1 |
| L-02 | Token factories can only hold the last deployed addresses | 3 |
| L-03 | Cashmanager will not work for collaterals with more than 18 decimals | 1 |

Total: 5 instances over 3 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| NC-01 | _beforeTokenTransfer will not fail if transaction was initiated by a sanctioned user but routed through an approved proxy | 2 |
| NC-02 | Token factories don't need to implement the IMulticall | 3 |
| NC-03 | Consider a not so strict versioning for your main interfaces | 6 |
| NC-04 | Move owner checks to a modifier | 15 |
| NC-05 | Use specific imports instead of just a global import | 24 |
| NC-06 | Solidity pragma versioning | 8 |
| NC-07 | Unnecessarily imports | 3 |

Total: 61 instances over 7 issues


### Informational
| |Issue|Instances|
|-|:-|:-:|
| I-01 | Remove unneccessary state allocation | 1 |
| I-02 | Don't mix uint and uint256 | 9 |
| I-03 | Add a license for JumpRateModelV2 | 1 |
| I-04 | Layout of a Solidity Source File | 3 |
| I-05 | Typos | 7 |
| I-06 | Missing documentation | 87 |

Total: 108 instances over 6 issues

---

## Low Risk Issues

### L-01 Check for a max percent for fees in completeRedemptions
At the moment the fees for completeRedemptions in contracts/cash/CashManager.sol can bet set arbitrarily. You should consider a check for the fees that they are not more than x% in the beginning of the function. The user redemptions could be tempered with a too high fee here.

```solidity
File: contracts/cash/CashManager.sol
707:   function completeRedemptions(
708:     address[] calldata redeemers,
709:     address[] calldata refundees,
710:     uint256 collateralAmountToDist,
711:     uint256 epochToService,
712:     uint256 fees
713:   ) external override updateEpoch onlyRole(MANAGER_ADMIN) {
```

### L-02 Token factories can only hold the last deployed addresses
All three token factories have three public addresses where they store the implementation, proxyAdmin and proxy. After every deployment deployXXX the state variables are updated, an event is fired and the "old" values are lost.

It would be better to store these informations in a mapping so it's possible to query for previous deployments.

For example have a struct that holds the informations, an counter for deployments and save these informations. With this it's possible to query all the informations for every deployment.

```solidity
File: contracts/cash/factory/CashFactory.sol
63:   struct Deployment {
64:     Cash implementation;
65:     ProxyAdmin proxyAdmin;
66:     TokenProxy tokenProxy;
67:   }
68:   uint256 currentDeployment;
69:   mapping(uint256 => Deployment) deployments;
```

### L-03 Cashmanager will not work for collaterals with more than 18 decimals
The current implementation for the CashManger does not allow collateral tokens with more than 18 decimals as the calculation will underflow and revert.

```solidity
File: contracts/cash/CashManager.sol
179:     decimalsMultiplier =
180:       10 **
181:         (IERC20Metadata(_cash).decimals() -
182:           IERC20Metadata(_collateral).decimals());
```

### L-04 Care of frontrunnable functions in CashManager
Some admin functions in CashManger could be frontrun from users if the new update sets a state worse than before for users.

Affected functions
- setMintFee => if the new fee > old fee, users could try to mint more tokens before the new fee will get activated
- setMinimumDepositAmount => users could try to get in with less tokens if the new minimumDepositAmount > old one
- setMintLimit => if the limit will be reduced one could mint some more before reduced
- setRedeemLimit => could be used to get out early if it is reduced by the admin

Consider to paus the contract before updating any of these functions.

---

## Non-critical Issues

### NC-01 _beforeTokenTransfer will not fail if transaction was initiated by a sanctioned user but routed through an approved proxy
The _beforeTokenTransfer for CashKYCSender and CashKYCSenderReceiver will not fail, if the transaction was initiated from an address that is not KYCd if it was routed through an KYCd proxy or anything.

```solidity
File: contracts/cash/token/CashKYCSender.sol
64:       _getKYCStatus(_msgSender()),

File: contracts/cash/token/CashKYCSenderReceiver.sol
64:       _getKYCStatus(_msgSender()),
```

### NC-02 Token factories don't need to implement the IMulticall
The CashKYCSenderReceiverFactory, CashKYCSenderFactory and CashFactory don't need to implement the IMulticall Interface. All the factories transfer the roles and owner to the guardian and so the factories should never need the arbitrary batched calls.

```solidity
File: contracts/cash/factory/CashFactory.sol
123:   function multiexcall(

File: contracts/cash/factory/CashKYCSenderFactory.sol
133:   function multiexcall(

File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol
133:   function multiexcall(
```

### NC-03 Consider a not so strict versioning for your main interfaces
At the moment your interfaces for the cash protocol under /contracts/cash/interfaces and the IOndoPriceOracleX's are only compatible with solidity 0.8.16. Consider a more open Version like ^0.8.0 so other projects can import your original interfaces and dont need to rewrite or change them if they use another version than 0.8.16.

```solidity
All under
File: contracts/cash/interfaces/Ixxxx.sol

File: contracts/lending/IOndoPriceOracle.sol

File: contracts/lending/IOndoPriceOracleV2.sol
```

### NC-04 Move owner checks to a modifier
It's better to use a modifier for simple owner checks for an easier inspection of functions. In a function it can be forgotten and with a modifier it's easier to see how the function is protected.

```solidity
File: contracts/lending/tokens/cCash/CCash.sol
268:   function _delegateCompLikeTo(address compLikeDelegatee) external {

File: contracts/lending/tokens/cCash/CCash.sol
150:   function sweepToken(EIP20NonStandardInterface token) external override {

File: contracts/lending/tokens/cCash/CTokenCash.sol
34:   function initialize(

File: contracts/lending/tokens/cCash/CTokenCash.sol
1060:   function _setPendingAdmin(

File: contracts/lending/tokens/cCash/CTokenCash.sol
1112:   function _setComptroller(

File: contracts/lending/tokens/cCash/CTokenCash.sol
1356:   function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {

File: contracts/lending/tokens/cCash/CTokenCash.sol
1378:   function setKYCRegistry(address _kycRegistry) external {

File: contracts/lending/tokens/cToken/CErc20.sol
268:   function _delegateCompLikeTo(address compLikeDelegatee) external {

File: contracts/lending/tokens/cToken/CTokenModified.sol
1063:   function _setPendingAdmin(

File: contracts/lending/tokens/cToken/CTokenModified.sol
1359:   function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {

File: contracts/lending/tokens/cToken/CTokenModified.sol
1381:   function setKYCRegistry(address _kycRegistry) external {

File: contracts/lending/tokens/cToken/CTokenModified.sol
34:   function initialize(

File: contracts/lending/tokens/cToken/CTokenModified.sol
1115:   function _setComptroller(

File: contracts/lending/tokens/cErc20ModifiedDelegator.sol
720:   function _setImplementation(

File: contracts/lending/JumpRateModelV2.sol
83:   function updateJumpRateModel(

```

### NC-05 Use specific imports instead of just a global import
For a better better developer experience it's better to use specific imports instead of just a global import. This helps to have a better overview what is realy needed and helps to have a clearer view of the contract.

```solidity
File: contracts/cash/factory/CashFactory.sol
File: contracts/cash/factory/CashKYCSenderFactory.sol
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol
File: contracts/cash/interfaces/IKYCRegistryClient.sol
File: contracts/cash/kyc/KYCRegistry.sol
File: contracts/cash/kyc/KYCRegistryClient.sol
File: contracts/cash/kyc/KYCRegistryClientConstructable.sol
File: contracts/cash/kyc/KYCRegistryClientInitializable.sol
File: contracts/cash/token/Cash.sol
File: contracts/cash/token/CashKYCSender.sol
File: contracts/cash/token/CashKYCSenderReceiver.sol
File: contracts/cash/CashManager.sol
File: contracts/cash/Proxy.sol
File: contracts/lending/tokens/cCash/CCash.sol
File: contracts/lending/tokens/cCash/CCashDelegate.sol
File: contracts/lending/tokens/cCash/CTokenCash.sol
File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
File: contracts/lending/tokens/cToken/CErc20.sol
File: contracts/lending/tokens/cToken/CTokenDelegate.sol
File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
File: contracts/lending/tokens/cToken/CTokenModified.sol
File: contracts/lending/JumpRateModelV2.sol
File: contracts/lending/OndoPriceOracle.sol
File: contracts/lending/OndoPriceOracleV2.sol
```

### NC-06 Solidity pragma versioning
Should be exactly same in all contracts. Currently you use ^0.8.10 for your c&fTokens but you can (and should) use the fixed Version 0.8.16 for them. All other contracts (except cErc20ModifiedDelegator.sol and JumpRateModelV2.sol) follow the 0.8.16 Version.

```solidity
File: contracts/lending/tokens/cCash/CCash.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cCash/CCashDelegate.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cCash/CTokenCash.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cToken/CErc20.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cToken/CTokenDelegate.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
2: pragma solidity ^0.8.10;

File: contracts/lending/tokens/cToken/CTokenModified.sol
2: pragma solidity ^0.8.10;
```

### NC-07 Unnecessarily imports
Some contracts have unnecessary imports that are never used and can be removed.

```solidity
File: contracts/cash/kyc/KYCRegistryClientConstructable.sol
17: import "contracts/cash/kyc/KYCRegistryClient.sol";

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
7: import "contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol";

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
7: import "contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol";

```


---

## Informational

### I-01 Remove unneccessary state allocation
In the constructor of CashManager you assigne the default state variable via
```solidity
currentEpoch = currentEpoch; 
```
This can be removed, or take it as an argument in the constructor.

### I-02 Don't mix uint and uint256
For better readbility and easier identification it's best to use uintX for specific uints. Instead of uint you should stick to uint256 or at least try to stay with one variant per file.

Mixing of uint and uint256 in file.
```solidity
File: contracts/lending/tokens/cCash/CCash.sol

File: contracts/lending/tokens/cCash/CTokenCash.sol

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

File: contracts/lending/tokens/cToken/CErc20.sol

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

File: contracts/lending/tokens/cToken/CTokenModified.sol

File: contracts/lending/tokens/cErc20ModifiedDelegator.sol

File: contracts/lending/IOndoPriceOracleV2.sol

File: contracts/lending/OndoPriceOracleV2.sol

```

### I-03 Add a license for JumpRateModelV2
JumpRateModelV2 is missing a license identifier. You should consider adding one.

### I-04 Layout of a Solidity Source File
For a better developer experience you should stick to the [solidity-docs](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html) specification how to structure a Solidity Source File.
Some files mixed up the order of the licencse => pragmas => imports => rest definition.

```solidity
File: contracts/cash/kyc/KYCRegistryClient.sol
16: 
17: import "contracts/cash/interfaces/IKYCRegistry.sol";
18: import "contracts/cash/interfaces/IKYCRegistryClient.sol";
19: 
20: pragma solidity 0.8.16;

File: contracts/cash/kyc/KYCRegistryClientConstructable.sol
17: import "contracts/cash/kyc/KYCRegistryClient.sol";
18: 
19: pragma solidity 0.8.16;
20: 
21: import "contracts/cash/kyc/KYCRegistryClient.sol";

File: contracts/cash/kyc/KYCRegistryClientInitializable.sol
17: import "contracts/cash/kyc/KYCRegistryClient.sol";
18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";
19: 
20: pragma solidity 0.8.16;
```

### I-05 Typos
There are some typos.

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol
38:   /// @notice Error for when caller attempts to set the KYC registry refernce
=> refernce = reference

File: contracts/cash/kyc/KYCRegistry.sol
62:    *         `kycRequirementGroup`. In order to sucessfully call this function,
=> sucessfully = successfully

File: contracts/cash/kyc/KYCRegistry.sol
205:    * @param addresses           Array of addresses being added as elligible
elligible = eligible

File: contracts/cash/kyc/KYCRegistry.sol
236:    * @param addresses           Array of addresses being added as elligible
elligible = eligible

File: contracts/cash/CashManager.sol
429:    *      BPS_DENOMINAOR (say 9999) and `mintFee` = 1,
=> BPS_DENOMINAOR = BPS_DENOMINATOR

File: contracts/lending/tokens/cCash/CTokenCash.sol
391:    * @dev This calculates interest accrued from the last checkpointed block
=> checkpointed = checkpoint

File: contracts/lending/tokens/cToken/CTokenModified.sol
391:    * @dev This calculates interest accrued from the last checkpointed block
=> checkpointed = checkpoint

```

### I-06 Missing documentation
The contracts are well documented and clear to read. Still it misses some documentation that could be added for a even better developer experience. Functions missing @param (or wrong param mentioned) / @return / @notice / @inheritdoc or is missing complete NatSpec.

```solidity
File: contracts/cash/factory/CashFactory.sol
123:   function multiexcall(

File: contracts/cash/factory/CashFactory.sol
143:   event CashDeployed(

File: contracts/cash/factory/CashKYCSenderFactory.sol
75:   function deployCashKYCSender(

File: contracts/cash/factory/CashKYCSenderFactory.sol
133:   function multiexcall(

File: contracts/cash/factory/CashKYCSenderFactory.sol
153:   event CashKYCSenderDeployed(

File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol
75:   function deployCashKYCSenderReceiver(

File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol
133:   function multiexcall(

File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol
153:   event CashKYCSenderReceiverDeployed(

File: contracts/cash/interfaces/ICashManager.sol
19:   function requestMint(uint256 collateralIn) external;

File: contracts/cash/interfaces/ICashManager.sol
22:   function claimMint(address user, uint256 epochToClaim) external;

File: contracts/cash/interfaces/ICashManager.sol
25:   function overrideExchangeRate(

File: contracts/cash/interfaces/ICashManager.sol
32:   function setAssetRecipient(address _assetRecipient) external;

File: contracts/cash/interfaces/ICashManager.sol
35:   function setFeeRecipient(address _feeRecipient) external;

File: contracts/cash/interfaces/ICashManager.sol
38:   function setAssetSender(address newAssetSender) external;

File: contracts/cash/interfaces/ICashManager.sol
41:   function setMinimumDepositAmount(uint256 _minimumDepositAmount) external;

File: contracts/cash/interfaces/ICashManager.sol
44:   function setMintFee(uint256 _mintFee) external;

File: contracts/cash/interfaces/ICashManager.sol
47:   function setMintExchangeRate(

File: contracts/cash/interfaces/ICashManager.sol
53:   function setMintExchangeRateDeltaLimit(

File: contracts/cash/interfaces/ICashManager.sol
58:   function requestRedemption(uint256 amountSharesTokenToRedeem) external;

File: contracts/cash/interfaces/ICashManager.sol
62:   function completeRedemptions(

File: contracts/cash/interfaces/ICashManager.sol
175:   event MintExchangeRateCheckFailed(

File: contracts/cash/interfaces/ICashManager.sol
233:   event MintRequested(

File: contracts/cash/interfaces/IKYCRegistryClient.sol
27:   function kycRequirementGroup() external view returns (uint256);

File: contracts/cash/interfaces/IKYCRegistryClient.sol
30:   function kycRegistry() external view returns (IKYCRegistry);

File: contracts/cash/interfaces/IKYCRegistryClient.sol
33:   function setKYCRequirementGroup(uint256 group) external;

File: contracts/cash/interfaces/IKYCRegistryClient.sol
36:   function setKYCRegistry(address registry) external;

File: contracts/cash/kyc/KYCRegistry.sol
128:   function getKYCStatus(

File: contracts/cash/token/CashKYCSender.sol
34:   function setKYCRequirementGroup(

File: contracts/cash/token/CashKYCSender.sol
40:   function setKYCRegistry(

File: contracts/cash/token/CashKYCSender.sol
46:   function initialize(

File: contracts/cash/token/CashKYCSender.sol
34:   function setKYCRequirementGroup(

File: contracts/cash/token/CashKYCSender.sol
40:   function setKYCRegistry(

File: contracts/cash/token/CashKYCSenderReceiver.sol
46:   function initialize(

File: contracts/lending/tokens/cCash/CTokenCash.sol
394:   function accrueInterest() public virtual override returns (uint) {

File: contracts/lending/tokens/cCash/CTokenCash.sol
1112:   function _setComptroller(

File: contracts/lending/tokens/cCash/CTokenCash.sol
1138:   function _setReserveFactor(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
119:   function isSanctioned(address addr) external view returns (bool);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
191:   event AccrueInterest(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
201:   event Mint(address minter, uint mintAmount, uint mintTokens);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
206:   event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
211:   event Borrow(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
221:   event RepayBorrow(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
232:   event LiquidateBorrow(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
245:   event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
250:   event NewAdmin(address oldAdmin, address newAdmin);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
255:   event NewComptroller(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
263:   event NewMarketInterestRateModel(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
271:   event NewReserveFactor(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
279:   event ReservesAdded(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
288:   event ReservesReduced(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
297:   event Transfer(address indexed from, address indexed to, uint amount);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
302:   event Approval(address indexed owner, address indexed spender, uint amount);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
306:   function transfer(address dst, uint amount) external virtual returns (bool);

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
308:   function transferFrom(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
314:   function approve(

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
180: abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {
=> all functions and events

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
390: abstract contract CErc20Interface is CErc20Storage {
=> all functions and events

File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
432:   event NewImplementation(address oldImplementation, address newImplementation);

File: contracts/lending/tokens/cToken/CErc20.sol
7:   function delegate(address delegatee) external;

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
117:   function isSanctioned(address addr) external view returns (bool);

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
178: abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {
=> all functions and events

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
388: abstract contract CErc20Interface is CErc20Storage {
=> all functions and events

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
430:   event NewImplementation(address oldImplementation, address newImplementation);

File: contracts/lending/tokens/cToken/CTokenModified.sol
394:   function accrueInterest() public virtual override returns (uint) {

File: contracts/lending/tokens/cToken/CTokenModified.sol
1115:   function _setComptroller(

File: contracts/lending/tokens/cToken/CTokenModified.sol
1141:   function _setReserveFactor(

File: contracts/lending/tokens/cErc20ModifiedDelegator.sol
148:   function isInterestRateModel() external pure returns (bool);

File: contracts/lending/IOndoPriceOracle.sol
28:   function setPrice(address fToken, uint256 price) external;

File: contracts/lending/IOndoPriceOracle.sol
30:   function setFTokenToCToken(address fToken, address cToken) external;

File: contracts/lending/IOndoPriceOracle.sol
32:   function setOracle(address newOracle) external;

File: contracts/lending/IOndoPriceOracleV2.sol
28:   function setPrice(address fToken, uint256 price) external;

File: contracts/lending/IOndoPriceOracleV2.sol
30:   function setFTokenToCToken(address fToken, address cToken) external;

File: contracts/lending/IOndoPriceOracleV2.sol
32:   function setOracle(address newOracle) external;

File: contracts/lending/IOndoPriceOracleV2.sol
78:   function setPriceCap(address fToken, uint256 value) external;

File: contracts/lending/IOndoPriceOracleV2.sol
80:   function setFTokenToChainlinkOracle(

File: contracts/lending/IOndoPriceOracleV2.sol
85:   function setMaxChainlinkOracleTimeDelay(

File: contracts/lending/IOndoPriceOracleV2.sol
89:   function setFTokenToOracleType(

File: contracts/lending/JumpRateModelV2.sol
14:   event NewInterestParams(

File: contracts/lending/OndoPriceOracle.sol
22:   function getUnderlyingPrice(address cToken) external view returns (uint256);

File: contracts/lending/OndoPriceOracle.sol
28:   function underlying() external view returns (address);

File: contracts/lending/OndoPriceOracle.sol
61:   function getUnderlyingPrice(

File: contracts/lending/OndoPriceOracleV2.sol
23:   function getUnderlyingPrice(address cToken) external view returns (uint256);

File: contracts/lending/OndoPriceOracleV2.sol
29:   function underlying() external view returns (address);

File: contracts/lending/OndoPriceOracleV2.sol
35:   function decimals() external view returns (uint8);

File: contracts/lending/OndoPriceOracleV2.sol
92:   function getUnderlyingPrice(

File: contracts/lending/OndoPriceOracleV2.sol
130:   function setPriceCap(

File: contracts/lending/OndoPriceOracleV2.sol
145:   function setFTokenToOracleType(

```
