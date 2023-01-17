## [QA-01] Use latest Solidity version

Solidity pragma versioning should be upgraded to latest available version. 
***

## [QA-02] Use stable pragma statement

Using a floating pragma `^0.8.10` statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
```
CCashDelegate.sol:
pragma solidity ^0.8.10;

CTokenDelegate.sol:
pragma solidity ^0.8.10;

JumpRateModelV2.sol:
pragma solidity ^0.5.16;

CCash.sol:
pragma solidity ^0.8.10;

CErc20.sol:
pragma solidity ^0.8.10;

CTokenInterfacesModifiedCash.sol:
pragma solidity ^0.8.10;

CTokenInterfacesModified.sol:
pragma solidity ^0.8.10;

cErc20ModifiedDelegator.sol:
pragma solidity ^0.5.12;

CTokenCash.sol:
pragma solidity ^0.8.10;

CTokenModified.sol::
pragma solidity ^0.8.10;
```
***

## [QA-03] Different pragma directives are used

Use one Solidity version on each contract.
***

## [QA-04] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
```
Proxy.sol:
18: import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

Cash.sol:
18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

CCashDelegate.sol:
4: import "./CCash.sol";

CTokenDelegate.sol:
4: import "./CErc20.sol";

CashKYCSender.sol:
18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
19: import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

OndoPriceOracle.sol:
17: import "./IOndoPriceOracle.sol";
18: import "contracts/lending/compound/Ownable.sol";

CashKYCSenderReceiver.sol:
18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
19: import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

CashFactory.sol:
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
20: import "contracts/cash/Proxy.sol";
21: import "contracts/cash/token/Cash.sol";
22: import "contracts/cash/interfaces/IMulticall.sol";

CashKYCSenderFactory.sol:
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
20: import "contracts/cash/Proxy.sol";
21: import "contracts/cash/token/CashKYCSender.sol";
22: import "contracts/cash/interfaces/IMulticall.sol";

CashKYCSenderReceiverFactory.sol:
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
20: import "contracts/cash/Proxy.sol";
21: import "contracts/cash/token/CashKYCSenderReceiver.sol";
22: import "contracts/cash/interfaces/IMulticall.sol";

JumpRateModelV2.sol:
3: import "./compound/InterestRateModel.sol";
4: import "./compound/SafeMath.sol";

KYCRegistry.sol:
18: import "contracts/cash/interfaces/IKYCRegistry.sol";
19: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
20: import "contracts/cash/external/chainalysis/ISanctionsList.sol";
21: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/EIP712.sol";
22: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol";

CCash.sol:
4: import "./CTokenCash.sol":

CErc20.sol:
4: import "./CTokenModified.sol";

OndoPriceOracleV2.sol:
17: import "./IOndoPriceOracleV2.sol";
18: import "contracts/cash/external/openzeppelin/contracts/access/Ownable.sol";
19: import "contracts/lending/chainlink/AggregatorV3Interface.sol";

CTokenInterfacesModifiedCash.sol:
4: import "contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol";
5: import "contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol";
6: import "contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol";
7: import "contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol";

CTokenInterfacesModified.sol:
4: import "contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol";
5: import "contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol";
6: import "contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol";
7: import "contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol";

CashManager.sol:
17: import "contracts/cash/interfaces/ICashManager.sol";
18: import "contracts/cash/interfaces/IMulticall.sol";
19: import "contracts/cash/token/Cash.sol";
20: import "contracts/cash/kyc/KYCRegistryClientConstructable.sol";
21: import "contracts/cash/external/openzeppelin/contracts/security/Pausable.sol";
22: import "contracts/cash/external/openzeppelin/contracts/token/IERC20.sol";
23: import "contracts/cash/external/openzeppelin/contracts/token/IERC20Metadata.sol";
24: import "contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol";
25: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
26: import "contracts/cash/external/openzeppelin/contracts/security/ReentrancyGuard.sol";

KYCRegistryClientConstructable.sol:
17: import "contracts/cash/kyc/KYCRegistryClient.sol";
21: import "contracts/cash/kyc/KYCRegistryClient.sol";

KYCRegistryClient.sol:
17: import "contracts/cash/interfaces/IKYCRegistry.sol";
18: import "contracts/cash/interfaces/IKYCRegistryClient.sol";

KYCRegistryClientInitializable.sol:
17: import "contracts/cash/kyc/KYCRegistryClient.sol";
18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";

CTokenCash.sol:
4: import "../cErc20Delegate/ComptrollerInterface.sol";
5: import "../cErc20Delegate/ErrorReporter.sol";
6: import "../cErc20Delegate/EIP20Interface.sol";
7: import "../cErc20Delegate/InterestRateModel.sol";
8: import "../cErc20Delegate/ExponentialNoError.sol";
9: import "./CTokenInterfacesModifiedCash.sol";

CTokenModified.sol:
4: import "../cErc20Delegate/ComptrollerInterface.sol";
5: import "../cErc20Delegate/ErrorReporter.sol";
6: import "../cErc20Delegate/EIP20Interface.sol";
7: import "../cErc20Delegate/InterestRateModel.sol";
8: import "../cErc20Delegate/ExponentialNoError.sol";
9: import "./CTokenInterfacesModified.sol";

IKYCRegistryClient.sol:
18: import "contracts/cash/interfaces/IKYCRegistry.sol";
```
***

## [QA-05] STOP USING V != 27 && V != 28 OR V == 27 || V == 28

```
KYCRegistry.sol:
87: require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
```

You can see this for [reference](https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg).
***

## [QA-06] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

```
Cash.sol:
22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

CashKYCSender.sol:
26: bytes32 public constant KYC_CONFIGURER_ROLE =
27:    keccak256("KYC_CONFIGURER_ROLE");

CashKYCSenderReceiver.sol:
26:  bytes32 public constant KYC_CONFIGURER_ROLE =
27:    keccak256("KYC_CONFIGURER_ROLE");

CashFactory.sol:
44:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45:  bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

CashKYCSenderFactory.sol:
44:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45:  bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

CashKYCSenderReceiverFactory.sol
44:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45:  bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

CashManager.sol:
122: bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
123: bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
124: bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

KYCRegistry.sol:
31: bytes32 public constant _APPROVAL_TYPEHASH =
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");

```
***

## [QA-07] Too many digits
Literals with many digits are difficult to read and review.
Is better to separate the digits with `_`.

```
JumpRateModelV2.sol:
29: uint public constant blocksPerYear = 2628000;
```
***

## [QA-08] Missing zero address validation

Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever.

```
CCash.sol:
193: function doTransferIn(
236: function doTransferOut(
268: function _delegateCompLikeTo(address compLikeDelegatee) external {

CErc20.sol:
193: function doTransferIn(
    	address from,
236: function doTransferOut(
      	address payable to,
268: function _delegateCompLikeTo(address compLikeDelegatee) external {

OndoPriceOracleV2.sol:
130: function setPriceCap(
    	address fToken,
145: function setFTokenToOracleType(
    	address fToken,
182: function setOracle(address newOracle) external override onlyOwner {
194: function setFTokenToCToken(
    	address fToken,
233: function setFTokenToChainlinkOracle(
    	address fToken,
    	address newChainlinkOracle

CashManager.sol:
241: function claimMint(
    	address user,
336: function setPendingMintBalance(
    	address user,
452: function setFeeRecipient(
    	address _feeRecipient
465: function setAssetRecipient(
    	address _assetRecipient
907: function setKYCRegistry(
    	address _kycRegistry
```

#### Recommendation

Check that the address is not zero.