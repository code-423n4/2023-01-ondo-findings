## QA Report


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | NO ZERO ADDRESS CHECK IN CONSTRUCTOR FOR KEY ADDRESSES | 3 |
| [L-2](#L-2) | USE OF FLOATING PRAGMA  | 26 |
| [L-3](#L-3) | USERS CAN'T CLAIM MINT AFTER DEPOSIT AND DEPEND ON `SETTER_ADMIN` TO SET THE EXCHANGE RATE FIRST | 1 |
| [L-4](#L-4) | MISSING CONTRACT-EXISTENCE CHECKS BEFORE LOW-LEVEL CALLS | 1 |
| [NC-1](#NC-1) | USE REQUIRE INSTEAD OF ASSERT | 3 |
| [NC-2](#NC-2) | STOP USING `V == 27 or V == 28` | 1 |
| [NC-3](#NC-3) | RECOMMENDED TO USE OWNABLE2STEP RATHER THAN OWNABLE | 1 |

### [L-1] NO ZERO ADDRESS CHECK IN CONSTRUCTOR FOR KEY ADDRESSES

In `CashManager.sol`, constructor is used to initialize different state variable and grant roles. But there is no Zero Address check on important variables like `managerAdmin` and `pauser`. 

It can result in serious consequences given they have been granted Important roles in the constructor.

*Instances:*
```solidity 
File:  cash/factory/CashFactory.sol

130:        address managerAdmin,
131:        address pauser,

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/factory/CashFactory.sol#L130-L131)

```solidity 
File:  lending/JumpRateModelV2.sol

66:        owner = owner_;

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L66)

### [L-2] USE OF FLOATING PRAGMA

*Instances: (26)*
* [JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L1)
* [IOndo.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/IOndo.sol#L2)
* [LinearTimelock.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/LinearTimelock.sol#L2)
* [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L2)
* [CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2)
* [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L2)
* [CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2)
* [CTokenInterfaces.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol#L2)
* [ComptrollerInterface.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol#L2)
* [EIP20Interface.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol#L2)
* [EIP20NonStandardInterface.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol#L2)
* [ErrorReporter.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol#L2)
* [ExponentialNoError.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol#L2)
* [InterestRateModel.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol#L2)
* [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L2)
* [CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2)
* [CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2)
* [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2)
* [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7)

### [L-3] USERS CAN'T CLAIM MINT AFTER DEPOSIT AND DEPEND ON `SETTER_ADMIN` TO SET THE EXCHANGE RATE FIRST

After depositing the collateral in `requestMint` method, User won't be able to claim the mint if `SETTER_ADMIN` is yet to set the Exchange Rate through `setMintExchangeRate` method.

There exist a Centralization risk because of this dependency where `SETTER_ADMIN` holds the power to stop User from minting despite already transfering the collateral.

```solidity 
File:  cash/CashManager.sol

249:        if (epochToExchangeRate[epochToClaim] == 0) {
250:            revert ExchangeRateNotSet();
251:        }

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L249-L251)

### [L-4] MISSING CONTRACT-EXISTENCE CHECKS BEFORE LOW-LEVEL CALLS

Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`

```solidity 
File:  lending/tokens/cErc20ModifiedDelegator.sol

1204:        (bool success, bytes memory returnData) = callee.delegatecall(data);

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1204)

### [NC-1] USE REQUIRE INSTEAD OF ASSERT

Assert should not be used except for tests, require should be used.

*Instances (3)*:
```solidity 

File:  cash/factory/CashFactory.sol

97:        assert(cashProxyAdmin.owner() == guardian);

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L97)

```solidity 

File:  cash/factory/CashKYCSenderReceiverFactory.sol

106:       assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106)

```solidity 

File:  cash/factory/CashKYCSenderFactory.sol

106:       assert(cashKYCSenderProxyAdmin.owner() == guardian);

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106)

### [NC-2] STOP USING `V != 27 && V != 28` OR `V == 27 || V == 28`

Reference: [Link](https://code4rena.com/reports/2022-11-non-fungible/#n-11-stop-using-v--27--v--28-or-v--27--v--28)

*Instance:*
```solidity 

File:  cash/kyc/KYCRegistry.sol

87:       require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L87)

### [NC-3] RECOMMENDED TO USE OWNABLE2STEP RATHER THAN OWNABLE

It is Recommended to use [Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) of Openzeppelin rather than Ownable.

```solidity
File: lending/OndoPriceOracleV2.sol

49:       contract OndoPriceOracleV2 is IOndoPriceOracleV2, Ownable {

```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L49)
