# QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Functions that send ether with a low level `call` are missing `nonReentrant` modifier | 1 |
| [L&#x2011;02] | Ownable uses single-step ownership transfer | 1 |
| [L&#x2011;03] | Missing zero address check in constructor  | 2 |
| [L&#x2011;04] | Consider adding initializer modifier to _initialize** functions  | 2 |

Total: 6 instances over 4 issues

## Non-critical

| N-C    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Missing params in NatSpec| 2 |
| [N&#x2011;02] | Use a safe pragma statement | 1 |
| [N&#x2011;03] | Typos in comments | 3 |
| [N&#x2011;04] | Use solidity's native units for dealing with time | 1 |
| [N&#x2011;05] | Constants should be defined rather than using magic numbers  | 1 |
| [N&#x2011;06] | Events are missing indexed fields | 3 |
| [N&#x2011;07] | Constants should be capitalized | 4 |

Total: 15 instances over 7 issues

## Low Risk

### \[L-01\] Function that sends ether with a low level `call` is missing `nonReentrant` modifier

File: `CashFactory.sol`

Add a nonReentrant modifier to this external function that is making a low level call in order to be sure 100% that reentrancy can not occur:

```solidity
(bool success, bytes memory ret) = address(exCallData[i].target).call{
 value: exCallData[i].value
}(exCallData[i].data);
```

Lines of code: 

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L128

### \[L-02\] Ownable uses single-step ownership transfer

File: `OndoPriceOracleV2.sol`

```solidity
import "contracts/cash/external/openzeppelin/contracts/access/Ownable.sol";
```

The OndoPriceOracleV2 contract inherits from Ownable and the owner is the one who can set couple of important features such as Oracle, Price of an fToken contract's underlying asset, etc.. The Ownable contract implements single-step ownership transfer, which means that if a wrong address was passed when transferring ownership or admin rights it can mean that role is lost forever.

Recommendation:

It is a best practice to use two-step ownership transfer pattern, meaning ownership transfer gets to a "pending" state and the new owner should claim his new rights, otherwise the old owner still has control of the contract. Consider using OpenZeppelin's Ownable2Step contract.

### \[L-03\] Missing zero address check in constructor 

File: `KycRegistry.sol`

```solidity
constructor(
    address admin,
    address _sanctionsList
  ) EIP712("OndoKYCRegistry", "1") {
    _grantRole(DEFAULT_ADMIN_ROLE, admin);
    _grantRole(REGISTRY_ADMIN, admin);
    sanctionsList = ISanctionsList(_sanctionsList);
```

Lines of code: 

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L52

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L53

Consider adding zero address check for the two address parameters in the constructor.

### \[L-04\] Consider adding initializer modifier to _initialize** functions

It's recommended to add initializer modifier to the initialize function of upgradable contracts to make sure they are called only once in the lifetime of a contract.

Files: `CCash.sol and CErc20.sol`

Lines of code respectively:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cCash/CCash.sol#L30

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cToken/CErc20.sol#L30

## Non-critical


### \[NC-01\] Missing params in NatSpec

Params `name` and `ticker` are not described in the NatSpec for event CashDeployed in `CashFactory.sol`

```solidity
 * @dev Event emitted when upgradable cash is deployed
   *
   * @param proxy             The address for the proxy contract
   * @param proxyAdmin        The address for the proxy admin contract
   * @param implementation    The address for the implementation contract
   */
  event CashDeployed(
    address proxy,
    address proxyAdmin,
    address implementation,
    string name,
    string ticker
  );
  ```
  
  ### \[NC-02\] Use a safe pragma statement

Always use stable pragma statement to lock the compiler version. Also there are different versions of the compiler used throughout the codebase, use only one. Finally consider upgrading the version to a newer one to use bugfixes and optimizations in the compiler.

Most of the contracts are using a fairly new version - 0.8.16, but for example JumpRateModelV2.sol is using floatable 0.5.16.

Update the pragma statement to stable 0.8.17

  ### \[NC-03\] Typos in comments

File: `KycRegistry.sol`

`sucessfully` -> `successfully`
`elligible` -> `eligible`

File: `JumpRateModelV2.sol`

`updateable` -> `updatable`


### \[NC-04\] Use solidity's native units for dealing with time

Solidity provides some native units for dealing with time. We can use the following units: seconds, minutes, hours, days, weeks and years. These units will convert to a uint of the number of seconds in that specific length of time.

File: `OndoPriceOracleV2.sol`

```solidity 
uint256 public maxChainlinkOracleTimeDelay = 90000;
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/OndoPriceOracleV2.sol#L77

Consider changing `90000` to `25 hours`

### \[NC-05\] Constants should be defined rather than using magic numbers 

File: `OndoPriceOracleV2.sol`

```solidity
TokenToChainlinkOracle[fToken].scaleFactor = (10 **
(36 - uint256(IERC20Like(underlying).decimals())
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/OndoPriceOracleV2.sol#L261

### \[NC-06\] Events are missing indexed fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields).  

File: `ICashManager.sol`

Events `FeeRecipientSet`, `AssetRecipientSet`, `AssetSenderSet` are missing indexed fields, consider adding them.

### \[NC-07\] Constants should be capitalized

Files: `CTokenInterfacesModified.sol and CTokenInterfacesModifiedCash.sol`

```solidity
uint internal constant borrowRateMaxMantissa = 0.0005e16;
...
uint internal constant reserveFactorMaxMantissa = 1e18;
```
Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36

```solidity
uint internal constant borrowRateMaxMantissa = 0.0005e16;
...
uint internal constant reserveFactorMaxMantissa = 1e18;
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L38
