
## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Upgradeable contract not initialized | 1 | 
| [L&#x2011;02] | Loss of precision | 1 | 
| [L&#x2011;03] | Owner can renounce while system is paused | 1 | 
| [L&#x2011;04] | `require()` should be used instead of `assert()` | 3 | 

Total: 6 instances over 4 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 4 | 
| [N&#x2011;02] | Import declarations should import specific identifiers, rather than the whole file | 45 | 
| [N&#x2011;03] | Duplicate import statements | 1 | 
| [N&#x2011;04] | The `nonReentrant` `modifier` should occur before all other modifiers | 3 | 
| [N&#x2011;05] | Adding a `return` statement when the function defines a named return variable, is redundant | 1 | 
| [N&#x2011;06] | `constant`s should be defined rather than using magic numbers | 3 | 
| [N&#x2011;07] | Numeric values having to do with time should use time units for readability | 1 | 
| [N&#x2011;08] | Events that mark critical parameter changes should contain both the old and the new value | 2 | 
| [N&#x2011;09] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 14 | 
| [N&#x2011;10] | Constant redefined elsewhere | 7 | 
| [N&#x2011;11] | Typos | 5 | 
| [N&#x2011;12] | Constructor visibility is ignored | 3 | 
| [N&#x2011;13] | File is missing NatSpec | 1 | 
| [N&#x2011;14] | NatSpec is incomplete | 16 | 
| [N&#x2011;15] | Consider using `delete` rather than assigning zero to clear values | 3 | 
| [N&#x2011;16] | Contracts should have full test coverage | 1 | 
| [N&#x2011;17] | Large or complicated code bases should implement fuzzing tests | 1 | 
| [N&#x2011;18] | Function ordering does not follow the Solidity style guide | 8 | 
| [N&#x2011;19] | Contract does not follow the Solidity style guide's suggested layout ordering | 14 | 
| [N&#x2011;20] | Interfaces should be indicated with an `I` prefix in the contract name | 6 | 

Total: 139 instances over 20 issues





## Low Risk Issues

### [L&#x2011;01]  Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/token/Cash.sol

/// @audit missing __ERC20PresetMinterPauser_init()
21:   contract Cash is ERC20PresetMinterPauserUpgradeable {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L21

### [L&#x2011;02]  Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/CashManager.sol

755         uint256 collateralAmountDue = (amountToDist * cashAmountReturned) /
756:          quantityBurned;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L755-L756

### [L&#x2011;03]  Owner can renounce while system is paused
The contract owner or single user with a role is not prevented from renouncing the role/ownership while the contract is paused, which would cause any user assets stored in the protocol, to be locked indefinitely

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/CashManager.sol

526     function pause() external onlyRole(PAUSER_ADMIN) {
527       _pause();
528:    }

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L526-L528

### [L&#x2011;04]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/factory/CashFactory.sol

97:       assert(cashProxyAdmin.owner() == guardian);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L97

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

106:      assert(cashKYCSenderProxyAdmin.owner() == guardian);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L106

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

106:      assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106

## Non-critical Issues

### [N&#x2011;01]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There are 4 instances of this issue:*

```solidity
File: contracts/cash/Proxy.sol

20:   contract TokenProxy is TransparentUpgradeableProxy {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/Proxy.sol#L20

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

22    contract CashKYCSenderReceiver is
23      ERC20PresetMinterPauserUpgradeable,
24:     KYCRegistryClientInitializable

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L22-L24

```solidity
File: contracts/cash/token/CashKYCSender.sol

22    contract CashKYCSender is
23      ERC20PresetMinterPauserUpgradeable,
24:     KYCRegistryClientInitializable

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L22-L24

```solidity
File: contracts/cash/token/Cash.sol

21:   contract Cash is ERC20PresetMinterPauserUpgradeable {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L21

### [N&#x2011;02]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 45 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

17:   import "contracts/cash/interfaces/ICashManager.sol";

18:   import "contracts/cash/interfaces/IMulticall.sol";

19:   import "contracts/cash/token/Cash.sol";

20:   import "contracts/cash/kyc/KYCRegistryClientConstructable.sol";

21:   import "contracts/cash/external/openzeppelin/contracts/security/Pausable.sol";

22:   import "contracts/cash/external/openzeppelin/contracts/token/IERC20.sol";

23:   import "contracts/cash/external/openzeppelin/contracts/token/IERC20Metadata.sol";

24:   import "contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol";

25:   import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";

26:   import "contracts/cash/external/openzeppelin/contracts/security/ReentrancyGuard.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L17

```solidity
File: contracts/cash/factory/CashFactory.sol

19:   import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

20:   import "contracts/cash/Proxy.sol";

21:   import "contracts/cash/token/Cash.sol";

22:   import "contracts/cash/interfaces/IMulticall.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L19

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

19:   import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

20:   import "contracts/cash/Proxy.sol";

21:   import "contracts/cash/token/CashKYCSender.sol";

22:   import "contracts/cash/interfaces/IMulticall.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L19

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

19:   import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

20:   import "contracts/cash/Proxy.sol";

21:   import "contracts/cash/token/CashKYCSenderReceiver.sol";

22:   import "contracts/cash/interfaces/IMulticall.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L19

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol

18:   import "contracts/cash/interfaces/IKYCRegistry.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IKYCRegistryClient.sol#L18

```solidity
File: contracts/cash/kyc/KYCRegistryClientConstructable.sol

17:   import "contracts/cash/kyc/KYCRegistryClient.sol";

21:   import "contracts/cash/kyc/KYCRegistryClient.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17

```solidity
File: contracts/cash/kyc/KYCRegistryClientInitializable.sol

17:   import "contracts/cash/kyc/KYCRegistryClient.sol";

18:   import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L17

```solidity
File: contracts/cash/kyc/KYCRegistryClient.sol

17:   import "contracts/cash/interfaces/IKYCRegistry.sol";

18:   import "contracts/cash/interfaces/IKYCRegistryClient.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L17

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

18:   import "contracts/cash/interfaces/IKYCRegistry.sol";

19:   import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";

20:   import "contracts/cash/external/chainalysis/ISanctionsList.sol";

21:   import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/EIP712.sol";

22:   import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L18

```solidity
File: contracts/cash/Proxy.sol

18:   import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/Proxy.sol#L18

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

18:   import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

19:   import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L18

```solidity
File: contracts/cash/token/CashKYCSender.sol

18:   import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

19:   import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L18

```solidity
File: contracts/cash/token/Cash.sol

18:   import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L18

```solidity
File: contracts/lending/OndoPriceOracle.sol

17:   import "./IOndoPriceOracle.sol";

18:   import "contracts/lending/compound/Ownable.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L17

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

17:   import "./IOndoPriceOracleV2.sol";

18:   import "contracts/cash/external/openzeppelin/contracts/access/Ownable.sol";

19:   import "contracts/lending/chainlink/AggregatorV3Interface.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L17

### [N&#x2011;03]  Duplicate import statements

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/kyc/KYCRegistryClientConstructable.sol

21:   import "contracts/cash/kyc/KYCRegistryClient.sol";

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L21

### [N&#x2011;04]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

201:      nonReentrant

244:    ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) {

668:      nonReentrant

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L201

### [N&#x2011;05]  Adding a `return` statement when the function defines a named return variable, is redundant

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/CashManager.sol

795:      return totalCashAmountRefunded;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L795

### [N&#x2011;06]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit 1e6
491:      uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L491

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit 27
/// @audit 28
87:       require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L87

### [N&#x2011;07]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There is 1 instance of this issue:*

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit 90000
77:     uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L77

### [N&#x2011;08]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 2 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit setPendingRedemptionBalance()
870       emit PendingRedemptionBalanceSet(
871         user,
872         epoch,
873         balance,
874         redemptionInfoPerEpoch[epoch].totalBurned
875:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L870-L875

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit setFTokenToOracleType()
150:      emit FTokenToOracleTypeSet(fToken, oracleType);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L150

### [N&#x2011;09]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it **doesn't save any gas** because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 14 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

122:    bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");

123:    bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");

124:    bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L122

```solidity
File: contracts/cash/factory/CashFactory.sol

44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L44

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L44

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

31      bytes32 public constant _APPROVAL_TYPEHASH =
32        keccak256(
33          "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
34:       );

36:     bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L31-L34

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

26      bytes32 public constant KYC_CONFIGURER_ROLE =
27:       keccak256("KYC_CONFIGURER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L26-L27

```solidity
File: contracts/cash/token/CashKYCSender.sol

26      bytes32 public constant KYC_CONFIGURER_ROLE =
27:       keccak256("KYC_CONFIGURER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L26-L27

```solidity
File: contracts/cash/token/Cash.sol

22:     bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L22

### [N&#x2011;10]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 7 instances of this issue:*

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit seen in contracts/cash/factory/CashFactory.sol 
44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

/// @audit seen in contracts/cash/factory/CashFactory.sol 
45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/// @audit seen in contracts/cash/factory/CashFactory.sol 
46:     bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L44

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit seen in contracts/cash/factory/CashKYCSenderFactory.sol 
44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

/// @audit seen in contracts/cash/factory/CashKYCSenderFactory.sol 
45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/// @audit seen in contracts/cash/factory/CashKYCSenderFactory.sol 
46:     bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44

```solidity
File: contracts/cash/token/CashKYCSender.sol

/// @audit seen in contracts/cash/token/CashKYCSenderReceiver.sol 
26      bytes32 public constant KYC_CONFIGURER_ROLE =
27:       keccak256("KYC_CONFIGURER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L26-L27

### [N&#x2011;11]  Typos

*There are 5 instances of this issue:*

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol

/// @audit refernce
38:     /// @notice Error for when caller attempts to set the KYC registry refernce

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IKYCRegistryClient.sol#L38

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit sucessfully
62:      *         `kycRequirementGroup`. In order to sucessfully call this function,

/// @audit elligible
205:     * @param addresses           Array of addresses being added as elligible

/// @audit elligible
236:     * @param addresses           Array of addresses being added as elligible

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L62

```solidity
File: contracts/lending/IOndoPriceOracleV2.sol

/// @audit assset
95:      * @dev Event for when a price cap is set on an fToken's underlying assset

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracleV2.sol#L95

### [N&#x2011;12]  Constructor visibility is ignored
Remove the `public` if using solidity at or above 0.7.0

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/factory/CashFactory.sol

53:     constructor(address _guardian) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L53

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

53:     constructor(address _guardian) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L53

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

53:     constructor(address _guardian) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L53

### [N&#x2011;13]  File is missing NatSpec

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/Proxy.sol


```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/Proxy.sol

### [N&#x2011;14]  NatSpec is incomplete

*There are 16 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit Missing: '@return'
500      *                         (in decimals of `collateral`)
501      */
502     function _getMintFees(
503       uint256 collateralAmount
504:    ) private view returns (uint256) {

/// @audit Missing: '@return'
831      *              of cash tokens for in a given epoch
832      */
833     function getBurnedQuantity(
834       uint256 epoch,
835       address user
836:    ) external view returns (uint256) {

/// @audit Missing: '@return'
947      *       3) value - eth value to call target with
948      */
949     function multiexcall(
950       ExCallData[] calldata exCallData
951     )
952       external
953       payable
954       override
955       nonReentrant
956       onlyRole(MANAGER_ADMIN)
957       whenPaused
958:      returns (bytes[] memory results)

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L500-L504

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit Missing: '@return'
121      *       3) value - eth value to call target with
122      */
123     function multiexcall(
124       ExCallData[] calldata exCallData
125:    ) external payable override onlyGuardian returns (bytes[] memory results) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L121-L125

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit Missing: '@param registry'
/// @audit Missing: '@param kycRequirementGroup'
57      /**
58       * @dev This function will deploy an upgradable instance of CashKYCSender
59       *
60       * @param name   The name of the token we want to deploy.
61       * @param ticker The ticker for the token we want to deploy.
62       *
63       * @return address The address of the proxy contract.
64       * @return address The address of the proxyAdmin contract.
65       * @return address The address of the implementation contract.
66       *
67       * @notice 1) Will automatically revoke all deployer roles granted to
68       *            address(this).
69       *         2) Will grant DEFAULT_ADMIN & PAUSER_ROLE(S) to `guardian`
70       *            address specified in constructor.
71       *         3) Will transfer ownership of the proxyAdmin to guardian
72       *            address.
73       *
74       */
75      function deployCashKYCSender(
76        string calldata name,
77        string calldata ticker,
78        address registry,
79        uint256 kycRequirementGroup
80:     ) external onlyGuardian returns (address, address, address) {

/// @audit Missing: '@return'
131      *       3) value - eth value to call target with
132      */
133     function multiexcall(
134       ExCallData[] calldata exCallData
135:    ) external payable override onlyGuardian returns (bytes[] memory results) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L57-L80

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit Missing: '@param registry'
/// @audit Missing: '@param kycRequirementGroup'
57      /**
58       * @dev This function will deploy an upgradable instance of CashKYCSenderReceiver
59       *
60       * @param name   The name of the token we want to deploy.
61       * @param ticker The ticker for the token we want to deploy.
62       *
63       * @return address The address of the proxy contract.
64       * @return address The address of the proxyAdmin contract.
65       * @return address The address of the implementation contract.
66       *
67       * @notice 1) Will automatically revoke all deployer roles granted to
68       *            address(this).
69       *         2) Will grant DEFAULT_ADMIN & PAUSER_ROLE(S) to `guardian`
70       *            address specified in constructor.
71       *         3) Will transfer ownership of the proxyAdmin to guardian
72       *            address.
73       *
74       */
75      function deployCashKYCSenderReceiver(
76        string calldata name,
77        string calldata ticker,
78        address registry,
79        uint256 kycRequirementGroup
80:     ) external onlyGuardian returns (address, address, address) {

/// @audit Missing: '@return'
131      *       3) value - eth value to call target with
132      */
133     function multiexcall(
134       ExCallData[] calldata exCallData
135:    ) external payable override onlyGuardian returns (bytes[] memory results) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L57-L80

```solidity
File: contracts/cash/kyc/KYCRegistryClient.sol

/// @audit Missing: '@return'
63       * @param account The address to check
64       */
65:     function _getKYCStatus(address account) internal view returns (bool) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L63-L65

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit Missing: '@return'
126      * @param account             Addresses to check KYC status for
127      */
128     function getKYCStatus(
129       uint256 kycRequirementGroup,
130       address account
131:    ) external view override returns (bool) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L126-L131

```solidity
File: contracts/lending/OndoPriceOracle.sol

/// @audit Missing: '@return'
59       *      set within `fTokenToCToken` and piggy back on an external price oracle.
60       */
61      function getUnderlyingPrice(
62        address fToken
63:     ) external view override returns (uint256) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L59-L63

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit Missing: '@return'
90       * @dev Only supports oracle prices denominated in USD
91       */
92      function getUnderlyingPrice(
93        address fToken
94:     ) external view override returns (uint256) {

/// @audit Missing: '@param value'
125     /**
126      * @notice Sets the price cap for the provided fToken's underlying asset
127      *
128      * @param fToken fToken contract address
129      */
130     function setPriceCap(
131       address fToken,
132       uint256 value
133:    ) external override onlyOwner {

/// @audit Missing: '@return'
275      * @dev This function is public for observability purposes only.
276      */
277     function getChainlinkOraclePrice(
278       address fToken
279:    ) public view returns (uint256) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L90-L94

### [N&#x2011;15]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

259:      mintRequestsPerEpoch[epochToClaim][user] = 0;

754:        redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;

790:        redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L259

### [N&#x2011;16]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;17]  Large or complicated code bases should implement fuzzing tests
Large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts, should implement [fuzzing tests](https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05). Fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and fuzzers, with properly and extensively-written invariants, can close this testing gap significantly.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;18]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 8 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit _scaleUp() came earlier
526:    function pause() external onlyRole(PAUSER_ADMIN) {

/// @audit transitionEpoch() came earlier
596:    function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {

/// @audit _checkAndUpdateRedeemLimit() came earlier
662     function requestRedemption(
663       uint256 amountCashToRedeem
664     )
665       external
666       override
667       updateEpoch
668       nonReentrant
669       whenNotPaused
670:      checkKYC(msg.sender)

/// @audit _processRefund() came earlier
803     function setAssetSender(
804       address newAssetSender
805:    ) external onlyRole(MANAGER_ADMIN) {

/// @audit _checkAddressesKYC() came earlier
949     function multiexcall(
950       ExCallData[] calldata exCallData
951     )
952       external
953       payable
954       override
955       nonReentrant
956       onlyRole(MANAGER_ADMIN)
957       whenPaused
958:      returns (bytes[] memory results)

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L526

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit _setFTokenToCToken() came earlier
233     function setFTokenToChainlinkOracle(
234       address fToken,
235       address newChainlinkOracle
236:    ) external override onlyOwner {

/// @audit _setFTokenToChainlinkOracle() came earlier
277     function getChainlinkOraclePrice(
278       address fToken
279:    ) public view returns (uint256) {

/// @audit getChainlinkOraclePrice() came earlier
309     function setMaxChainlinkOracleTimeDelay(
310       uint256 _maxChainlinkOracleTimeDelay
311:    ) external override onlyOwner {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L233-L236

### [N&#x2011;19]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 14 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit function setEpochDuration came earlier
557     modifier updateEpoch() {
558       transitionEpoch();
559       _;
560:    }

/// @audit function setPendingRedemptionBalance came earlier
885     modifier checkKYC(address account) {
886       _checkKYC(account);
887       _;
888:    }

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L557-L560

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit function multiexcall came earlier
143     event CashDeployed(
144       address proxy,
145       address proxyAdmin,
146       address implementation,
147       string name,
148       string ticker
149:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L143-L149

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit function multiexcall came earlier
153     event CashKYCSenderDeployed(
154       address proxy,
155       address proxyAdmin,
156       address implementation,
157       string name,
158       string ticker,
159       address registry
160:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L153-L160

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit function multiexcall came earlier
153     event CashKYCSenderReceiverDeployed(
154       address proxy,
155       address proxyAdmin,
156       address implementation,
157       string name,
158       string ticker,
159       address registry
160:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L153-L160

```solidity
File: contracts/cash/interfaces/ICashManager.sol

/// @audit function completeRedemptions came earlier
76:     event FeeRecipientSet(address oldFeeRecipient, address newFeeRecipient);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/ICashManager.sol#L76

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol

/// @audit function setKYCRegistry came earlier
48:     event KYCRegistrySet(address oldRegistry, address newRegistry);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IKYCRegistryClient.sol#L48

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit function removeKYCAddresses came earlier
195     event RoleAssignedToKYCGroup(
196       uint256 indexed kycRequirementGroup,
197       bytes32 indexed role
198:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L195-L198

```solidity
File: contracts/lending/IOndoPriceOracle.sol

/// @audit function setOracle came earlier
41      event FTokenToCTokenSet(
42        address indexed fToken,
43        address oldCToken,
44        address newCToken
45:     );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracle.sol#L41-L45

```solidity
File: contracts/lending/IOndoPriceOracleV2.sol

/// @audit function setOracle came earlier
41      event FTokenToCTokenSet(
42        address indexed fToken,
43        address oldCToken,
44        address newCToken
45:     );

/// @audit event CTokenOracleSet came earlier
71      enum OracleType {
72        UNINITIALIZED,
73        MANUAL,
74        COMPOUND,
75        CHAINLINK
76:     }

/// @audit function setFTokenToOracleType came earlier
101     event PriceCapSet(
102       address indexed fToken,
103       uint256 oldPriceCap,
104       uint256 newPriceCap
105:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracleV2.sol#L41-L45

```solidity
File: contracts/lending/OndoPriceOracle.sol

/// @audit function underlying came earlier
41      CTokenOracle public cTokenOracle =
42:       CTokenOracle(0x65c816077C29b557BEE980ae3cC2dCE80204A0C5);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L41-L42

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit function decimals came earlier
51      CTokenOracle public cTokenOracle =
52:       CTokenOracle(0x65c816077C29b557BEE980ae3cC2dCE80204A0C5);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L51-L52

### [N&#x2011;20]  Interfaces should be indicated with an `I` prefix in the contract name

*There are 6 instances of this issue:*

```solidity
File: contracts/lending/IOndoPriceOracle.sol

18:   interface PriceOracle {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracle.sol#L18

```solidity
File: contracts/lending/IOndoPriceOracleV2.sol

18:   interface PriceOracle {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracleV2.sol#L18

```solidity
File: contracts/lending/OndoPriceOracle.sol

21:   interface CTokenOracle {

27:   interface CTokenLike {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L21

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

22:   interface CTokenOracle {

28:   interface CTokenLike {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L22


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 6 | 

Total: 6 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `constant`s should be defined rather than using magic numbers | 1 | 
| [N&#x2011;02] | Event is missing `indexed` fields | 38 | 

Total: 39 instances over 2 issues





## Low Risk Issues

### [L&#x2011;01]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 6 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit (valid but excluded finding)
456:      feeRecipient = _feeRecipient;

/// @audit (valid but excluded finding)
469:      assetRecipient = _assetRecipient;

/// @audit (valid but excluded finding)
807:      assetSender = newAssetSender;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L456

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
54:       guardian = _guardian;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L54

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit (valid but excluded finding)
54:       guardian = _guardian;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L54

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit (valid but excluded finding)
54:       guardian = _guardian;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54

## Non-critical Issues

### [N&#x2011;01]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There is 1 instance of this issue:*

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit 36 - (valid but excluded finding)
261:        (36 -

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L261

### [N&#x2011;02]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 38 instances of this issue:*

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
143     event CashDeployed(
144       address proxy,
145       address proxyAdmin,
146       address implementation,
147       string name,
148       string ticker
149:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L143-L149

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit (valid but excluded finding)
153     event CashKYCSenderDeployed(
154       address proxy,
155       address proxyAdmin,
156       address implementation,
157       string name,
158       string ticker,
159       address registry
160:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L153-L160

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit (valid but excluded finding)
153     event CashKYCSenderReceiverDeployed(
154       address proxy,
155       address proxyAdmin,
156       address implementation,
157       string name,
158       string ticker,
159       address registry
160:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L153-L160

```solidity
File: contracts/cash/interfaces/ICashManager.sol

/// @audit (valid but excluded finding)
76:     event FeeRecipientSet(address oldFeeRecipient, address newFeeRecipient);

/// @audit (valid but excluded finding)
84:     event AssetRecipientSet(address oldAssetRecipient, address newAssetRecipient);

/// @audit (valid but excluded finding)
92:     event AssetSenderSet(address oldAssetSender, address newAssetSender);

/// @audit (valid but excluded finding)
102:    event MinimumDepositAmountSet(uint256 oldMinimum, uint256 newMinimum);

/// @audit (valid but excluded finding)
111:    event MinimumRedeemAmountSet(uint256 oldRedeemMin, uint256 newRedeemMin);

/// @audit (valid but excluded finding)
121:    event MintFeeSet(uint256 oldFee, uint256 newFee);

/// @audit (valid but excluded finding)
133     event MintExchangeRateSet(
134       uint256 indexed epoch,
135       uint256 oldRate,
136       uint256 newRate
137:    );

/// @audit (valid but excluded finding)
151     event MintExchangeRateOverridden(
152       uint256 indexed epoch,
153       uint256 oldRate,
154       uint256 newRate,
155       uint256 lastSetMintExchangeRate
156:    );

/// @audit (valid but excluded finding)
164:    event ExchangeRateDeltaLimitSet(uint256 oldLimit, uint256 newLimit);

/// @audit (valid but excluded finding)
175     event MintExchangeRateCheckFailed(
176       uint256 indexed epoch,
177       uint256 lastEpochRate,
178       uint256 newRate
179:    );

/// @audit (valid but excluded finding)
189:    event MintLimitSet(uint256 oldLimit, uint256 newLimit);

/// @audit (valid but excluded finding)
199:    event RedeemLimitSet(uint256 oldLimit, uint256 newLimit);

/// @audit (valid but excluded finding)
209:    event EpochDurationSet(uint256 oldDuration, uint256 newDuration);

/// @audit (valid but excluded finding)
218     event RedemptionRequested(
219       address indexed user,
220       uint256 cashAmountIn,
221       uint256 indexed epoch
222:    );

/// @audit (valid but excluded finding)
233     event MintRequested(
234       address indexed user,
235       uint256 indexed epoch,
236       uint256 collateralAmountDeposited,
237       uint256 depositAmountAfterFee,
238       uint256 feeAmount
239:    );

/// @audit (valid but excluded finding)
250     event RedemptionCompleted(
251       address indexed user,
252       uint256 cashAmountRequested,
253       uint256 collateralAmountReturned,
254       uint256 indexed epoch
255:    );

/// @audit (valid but excluded finding)
268     event MintCompleted(
269       address indexed user,
270       uint256 cashAmountOut,
271       uint256 collateralAmountDeposited,
272       uint256 exchangeRate,
273       uint256 indexed epochClaimedFrom
274:    );

/// @audit (valid but excluded finding)
284     event RedemptionFeesCollected(
285       address indexed beneficiary,
286       uint256 collateralAmountOut,
287       uint256 indexed epoch
288:    );

/// @audit (valid but excluded finding)
297     event RefundIssued(
298       address indexed user,
299       uint256 cashAmountOut,
300       uint256 indexed epoch
301:    );

/// @audit (valid but excluded finding)
311     event PendingMintBalanceSet(
312       address indexed user,
313       uint256 indexed epoch,
314       uint256 oldBalance,
315       uint256 newBalance
316:    );

/// @audit (valid but excluded finding)
327     event PendingRedemptionBalanceSet(
328       address indexed user,
329       uint256 indexed epoch,
330       uint256 balance,
331       uint256 totalBurned
332:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/ICashManager.sol#L76

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol

/// @audit (valid but excluded finding)
48:     event KYCRegistrySet(address oldRegistry, address newRegistry);

/// @audit (valid but excluded finding)
56      event KYCRequirementGroupSet(
57        uint256 oldRequirementGroup,
58        uint256 newRequirementGroup
59:     );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IKYCRegistryClient.sol#L48

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
207     event KYCAddressesAdded(
208       address indexed sender,
209       uint256 indexed kycRequirementGroup,
210       address[] addresses
211:    );

/// @audit (valid but excluded finding)
238     event KYCAddressesRemoved(
239       address indexed sender,
240       uint256 indexed kycRequirementGroup,
241       address[] addresses
242:    );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L207-L211

```solidity
File: contracts/lending/IOndoPriceOracle.sol

/// @audit (valid but excluded finding)
41      event FTokenToCTokenSet(
42        address indexed fToken,
43        address oldCToken,
44        address newCToken
45:     );

/// @audit (valid but excluded finding)
54      event UnderlyingPriceSet(
55        address indexed fToken,
56        uint256 oldPrice,
57        uint256 newPrice
58:     );

/// @audit (valid but excluded finding)
66:     event CTokenOracleSet(address oldOracle, address newOracle);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracle.sol#L41-L45

```solidity
File: contracts/lending/IOndoPriceOracleV2.sol

/// @audit (valid but excluded finding)
41      event FTokenToCTokenSet(
42        address indexed fToken,
43        address oldCToken,
44        address newCToken
45:     );

/// @audit (valid but excluded finding)
54      event UnderlyingPriceSet(
55        address indexed fToken,
56        uint256 oldPrice,
57        uint256 newPrice
58:     );

/// @audit (valid but excluded finding)
66:     event CTokenOracleSet(address oldOracle, address newOracle);

/// @audit (valid but excluded finding)
101     event PriceCapSet(
102       address indexed fToken,
103       uint256 oldPriceCap,
104       uint256 newPriceCap
105:    );

/// @audit (valid but excluded finding)
114     event ChainlinkOracleSet(
115       address indexed fToken,
116       address oldOracle,
117       address newOracle
118:    );

/// @audit (valid but excluded finding)
126     event MaxChainlinkOracleTimeDelaySet(
127       uint256 oldMaxTimeDelay,
128       uint256 newMaxTimeDelay
129:    );

/// @audit (valid but excluded finding)
137:    event FTokenToOracleTypeSet(address indexed fToken, OracleType oracleType);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracleV2.sol#L41-L45
