
### 1st BUG
Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1695 => comp.transfer(user, amount);
```
#### Tools used
Manual VS Code review

### 2nd BUG
Unspecific Compiler Version Pragma

#### Impact
Issue Information: 
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/AccessControl.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/IAccessControl.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/IAccessControlEnumerable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/Ownable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Proxy.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::4 => pragma solidity ^0.8.2;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/IBeacon.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/Proxy.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/draft-IERC1822.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/security/Pausable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/security/ReentrancyGuard.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/IERC20.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/IERC20Metadata.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Address.sol::4 => pragma solidity ^0.8.1;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Context.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Counters.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/ERC165.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/IERC165.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/StorageSlot.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/EIP712.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/AccessControlEnumerableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/IAccessControlEnumerableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/IAccessControlUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol::4 => pragma solidity ^0.8.2;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/security/PausableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20BurnableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PausableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/IERC20MetadataUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721BurnableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PausableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/IERC721EnumerableUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/IERC721MetadataUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/IERC721ReceiverUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/IERC721Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol::4 => pragma solidity ^0.8.1;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/CounterUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/ERC165Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/IERC165Upgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::4 => pragma solidity ^0.8.0;
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/ComptrollerInterface.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/ComptrollerStorage.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/ErrorReporter.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/ExponentialNoError.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/InterestRateModel.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/Ownable.sol::2 => pragma solidity ^0.6.12;
2023-01-ondo/contracts/lending/compound/PriceOracle.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/ReentrancyGuard.sol::1 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/SafeMath.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/Unitroller.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/chainlink/AggregatorValidatorInterface.sol::2 => pragma solidity ^0.6.12;
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/governance/GovernanceBravoDelegator.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoInterfaces.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::7 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::184 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/tokens/CarefulMath.sol::1 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/tokens/EIP20Interface.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/tokens/Exponential.sol::1 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/tokens/LegacyInterestRateModel.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/compound/tokens/cErc20Delegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/compound/tokens/cErc20Delegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/compound/tokens/cErc20Delegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/compound/tokens/cErc20Delegator.sol::579 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/compound/tokens/cToken.sol::1 => pragma solidity ^0.5.8;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::3 => pragma solidity ^0.6.12;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapConfig.sol::3 => pragma solidity ^0.6.12;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapLib.sol::3 => pragma solidity ^0.6.12;
2023-01-ondo/contracts/lending/ondo/ondo-token/IOndo.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol::2 => pragma solidity ^0.8.10;
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
#### Tools used
Manual VS Code review

### 3rd BUG
Do not use Deprecated Library Functions

#### Impact
Issue Information: 
The usage of deprecated library functions should be discouraged.

This issue is mostly related to OpenZeppelin libraries.

Example
🤦 Bad:

use SafeERC20 for IERC20;

// ...

IERC20(token).safeApprove(spender, value);
🚀 Good:

use SafeERC20 for IERC20;

// ...

IERC20(token).safeIncreaseAllowance(spender, value);

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/AccessControl.sol::218 => function _setupRole(bytes32 role, address account) internal virtual {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol::51 => function safeApprove(
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol::248 => function _setupRole(bytes32 role, address account) internal virtual {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::66 => _setupRole(DEFAULT_ADMIN_ROLE, _msgSender());
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::68 => _setupRole(MINTER_ROLE, _msgSender());
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::69 => _setupRole(PAUSER_ROLE, _msgSender());
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::81 => _setupRole(DEFAULT_ADMIN_ROLE, _msgSender());
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::83 => _setupRole(MINTER_ROLE, _msgSender());
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::84 => _setupRole(PAUSER_ROLE, _msgSender());
```
#### Tools used
Manual VS Code review