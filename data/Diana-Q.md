
## 01 Require() should be used instead of assert()

Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.

_There are 3 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```
File: contracts/cash/factory/CashFactory.sol

97: assert(cashProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
File: contracts/cash/factory/CashKYCSenderFactory.sol

106: assert(cashKYCSenderProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian)
```

-----

## 02 Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

_There are 5 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol

```
File: contracts/cash/Proxy.sol

20: contract TokenProxy is TransparentUpgradeableProxy {
25: ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```
File: contracts/cash/token/Cash.sol

21: contract Cash is ERC20PresetMinterPauserUpgradeable {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```
File: contracts/cash/token/CashKYCSender.sol

23: ERC20PresetMinterPauserUpgradeable,
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```
File: contracts/cash/token/CashKYCSenderReceiver.sol

23: ERC20PresetMinterPauserUpgradeable,
```

--------

## 03 Missing events for functions that change critical parameters 

The afunctions that change critical parameters should emit events. Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services. The alternative of directly querying on-chain contract state for such changes is not considered practical for most users/usages.

Missing events and timelocks do not promote transparency and if such changes immediately affect users’ perception of fairness or trustworthiness, they could exit the protocol causing a reduction in liquidity which could negatively impact protocol TVL and reputation.

### Recommended Mitigation Steps

Add events to all functions that change critical parameters.

_There are 2 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

896: function setKYCRequirementGroup(
907: function setKYCRegistry(
```

---------

## 04 Interfaces should be moved to seperate files

_There are 14 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```
File: contracts/lending/tokens/cCash/CCash.sol

6: interface CompLike
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```
File: contracts/lending/tokens/cErc20ModifiedDelegator.sol

9: interface ComptrollerInterface
144: interface InterestRateModel
298: interface ISanctionsList
309: interface IKYCRegistry
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```
File: contracts/lending/OndoPriceOracle.sol

21: interface CTokenOracle
27: interface CTokenLike
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```
File: contracts/lending/OndoPriceOracleV2.sol

22: interface CTokenOracle
28: interface CTokenLike
34: interface IERC20Like
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```
File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

118: interface ISanctionsList
127: interface IKYCRegistry
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

```
File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

116: interface ISanctionsList
125: interface IKYCRegistry
```

--------

## 05 Non-library or interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

_There are 16 instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol

```
File: contracts/cash/token/CashKYCSenderReceiver.sol

16: pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```
File: contracts/lending/JumpRateModelV2.sol

1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```
File: contracts/lending/tokens/cErc20ModifiedDelegator.sol

7: pragma solidity ^0.5.12;
138: pragma solidity ^0.5.12;
181: pragma solidity ^0.5.12;
296: pragma solidity ^0.5.16;
324: pragma solidity ^0.5.16;
647: pragma solidity ^0.5.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```
File: contracts/lending/tokens/cCash/CCash.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol

```
File: contracts/lending/tokens/cCash/CCashDelegate.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```
File: contracts/lending/tokens/cCash/CTokenCash.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

```
File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```
File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```
File: contracts/lending/tokens/cToken/CErc20.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol

```
File: contracts/lending/tokens/cToken/CTokenDelegate.sol

2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```
File: contracts/lending/tokens/cToken/CTokenModified.sol

2: pragma solidity ^0.8.10;
```

----------

## 06 Use named imports instead of plain 'import file.sol'

For instance, you use regular imports such as:

[Proxy.sol#L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L18)

```
import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";
```

Instead of this, use named imports such as:

```
import {TransparentUpgradeableProxy.sol} from "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";
```

_There are 71 instances of this issue in the following In-scope contracts:_

[CashManager.sol#L17-L26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L17-L26), [Proxy.sol#L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L18), [CashFactory.sol#L19-L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L19-L22), [CashKYCSenderFactory.sol#L19-L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L19-L22), [CashKYCSenderReceiverFactory.sol#L19-L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L19-L22), [IKYCRegistryClient.sol#L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol#L18), [KYCRegistry.sol#L18-L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L18-L22), [KYCRegistryClient.sol#L17-L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L17-L18), [KYCRegistryClientConstructable.sol#L17-L21](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17-L21), [KYCRegistryClientInitializable.sol#L17-L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L17-L18), [Cash.sol#L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L18), [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol), [CashKYCSenderReceiver.sol#L18-L19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L18-L19), [JumpRateModelV2.sol#L3-L4](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L3-L4), [OndoPriceOracle.sol#L17-L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L17-L18), [OndoPriceOracleV2.sol#L17-L19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L17-L19), [CCash.sol#L4](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L4), [CCashDelegate.sol#L4](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L4), [CTokenCash.sol#L4-L9](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L4-L9), [CTokenInterfacesModifiedCash.sol#L4-L7](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L4-L7), [CErc20.sol#L4](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L4), [CTokenDelegate.sol#L4](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L4), [CTokenInterfacesModified.sol#L4-L7](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L4-L7), [CTokenModified.sol#L4-L9](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L4-L9)

-------------

## 07 Include return parameters in natspec comment

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

[https://docs.soliditylang.org/en/v0.8.15/natspec-format.html](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

If Return parameters are declared, you must prefix them with `/// @return`.

_There are several instances of this issue_

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```
File: contracts/cash/CashManager.sol

949: function multiexcall(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```
File: contracts/cash/factory/CashKYCSenderFactory.sol

133: function multiexcall(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

133: function multiexcall(
```

-----

## 08 Use a more recent version of solidity

It's a best practice to use the latest compiler version.
List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

This issue exists in all the In-scope contracts

---------
