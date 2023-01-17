
### 1. REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
97: assert(cashProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
106: assert(cashKYCSenderProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```


### 2. UPGRADEABLE CONTRACT IS MISSING A `__GAP[50]` STORAGE VARIABLE TO ALLOW FOR NEW STORAGE VARIABLES IN LATER VERSIONS

*Number of Instances Identified: 4*

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol

```
20-26: contract TokenProxy is TransparentUpgradeableProxy {
  constructor(
    address _logic,
    address _admin,
    bytes memory _data
  ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
}
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
21: contract Cash is ERC20PresetMinterPauserUpgradeable
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
22-24: contract CashKYCSender is
  ERC20PresetMinterPauserUpgradeable,
  KYCRegistryClientInitializable
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
22-24: contract CashKYCSenderReceiver is
  ERC20PresetMinterPauserUpgradeable,
  KYCRegistryClientInitializable
```


### 3. MISSING EVENTS FOR FUNCTIONS THAT CHANGE CRITICAL PARAMETERS

The functions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
526-528: function pause() external onlyRole(PAUSER_ADMIN) {
    _pause();
  }
533-535: function unpause() external onlyRole(MANAGER_ADMIN) {
    _unpause();
  }
896-900: function setKYCRequirementGroup(
    uint256 _kycRequirementGroup
  ) external override onlyRole(MANAGER_ADMIN) {
    _setKYCRequirementGroup(_kycRequirementGroup);
  }
907-911: function setKYCRegistry(
    address _kycRegistry
  ) external override onlyRole(MANAGER_ADMIN) {
    _setKYCRegistry(_kycRegistry);
  }
```



### 4. EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

*Number of Instances Identified: 14*

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
122: bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
123: bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
124: bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```
31-34: bytes32 public constant _APPROVAL_TYPEHASH =
    keccak256(
      "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
    );
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
26-27: bytes32 public constant KYC_CONFIGURER_ROLE =
    keccak256("KYC_CONFIGURER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
26-27: bytes32 public constant KYC_CONFIGURER_ROLE =
    keccak256("KYC_CONFIGURER_ROLE");
```


### 5. FOR MODERN AND MORE READABLE CODE; UPDATE IMPORT USAGES

*Number of Instances Identified: 24*

### Description

Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

### Recommendation

`import {contract1 , contract2} from "filename.sol";`

#### POC

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol


### 6. INTERFACES SHOULD BE MOVED TO SEPARATE FILES

*Number of Instances Identified: 14*

Most of the other interfaces in this project are in their own file. The interfaces below do not follow this pattern:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
21: interface CTokenOracle
27: interface CTokenLike
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
22: interface CTokenOracle
28: interface CTokenLike
34: interface IERC20Like
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```
9: interface ComptrollerInterface
144: interface InterestRateModel
298: interface ISanctionsList
309: interface IKYCRegistry
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```
6: interface CompLike
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```
118: interface ISanctionsList
127: interface IKYCRegistry
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

```
116: interface ISanctionsList
125: interface IKYCRegistry
```


### 7. USE LATEST SOLIDITY VERSION


When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol


### 8. FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

The following contracts are using Floating pragma versions

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

### 9. INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

*Number of Instances Identified: 6*

### Description

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the `@return` tag, they do incomplete analysis.

### Recommendation

Include return parameters in NatSpec comments

Recommendation Code Style:

```
 /// @return include Returns parameters
```

**POC**: 
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
502: function _getMintFees
514: function _scaleUp(uint256 amount)
949: function multiexcall
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
123: function multiexcall
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
13#: function multiexcall
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
133: function multiexcall
```


### 10. ADDING A RETURN STATEMENT WHEN THE FUNCTION DEFINES A NAMED RETURN VARIABLE, IS REDUNDANT

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
781-784: function _processRefund(
    address[] calldata refundees,
    uint256 epochToService
  ) private returns (uint256 totalCashAmountRefunded)
```

