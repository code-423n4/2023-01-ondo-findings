## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Project has NPM Dependency which uses a vulnerable version : `axios`| 1 |
|[L-02]| Project has NPM Dependency which uses a vulnerable version : `node-fetch`| 1 |
|[L-03]| Loss of precision due to rounding| 1 |
|[L-04]| Missing Event for critical parameters init and change| 6 |
|[L-05]| Use `2Step` change architecture instead of ` setfeeRecipient ` and `setAssetRecipient`| 1 |
|[L-06]| Stack too deep when compiling |  |
|[L-07]|There is a risk that the `setMintFee` variable is accidentally execute to 0 with `setMintFee` function| 1 |
|[L-08]| No Storage Gap for `KYCRegistryClientInitializable`| 1 |
|[L-09]| The nonReentrant modifier should occur before all other modifiers| 1 |
|[L-10]| initialize() functions can be called by anybody| 4 |
|[L-11]| Use `uint256` instead `uint` | 336 |

Total 11 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Insufficient coverage |All Contracts|
| [N-02] |NatSpec comments should be increased in contracts  |All Contracts|
| [N-03] |`Function writing` that does not comply with the `Solidity Style Guide`| All Contracts |
| [N-04] |Add a timelock to critical functions  | 1 |
| [N-05] |Include return parameters in NatSpec comments | All contracts |
| [N-06] |Keccak Constant values should used to immutable rather than constant| 14 |
| [N-07] |Mark visibility of initialize(...) functions as ``external`` | 4 |
| [N-08] |Use underscores for number literals | 2 |
| [N-09] |Lack of event emission after critical `initialize()` function| 4 |
| [N-10] |`Empty blocks` should be _removed_ or _Emit_ something  | 3 |
| [N-11] |Tokens accidentally sent to the contract cannot be recovered|  |
| [N-12] |Assembly Codes Specific – Should Have Comments |7  |
| [N-13] |Use a single file for all system-wide constants | 34 |
| [N-14] |Take advantage of Custom Error's return value property | 49 |
| [N-15] |Repeated validation logic can be converted into a function modifier | 6 |
| [N-16] |Avoid _shadowing_ `inherited state variables` | 1 |
| [N-17] |Use a more recent version of OpenZeppelin dependencies| 1 |
| [N-18] |Use `require` instead of `assert` | 3 |
| [N-19] |For modern and more readable code; update import usages|All Contracts |
| [N-20] |Compliance with Solidity Style rules in Constant expressions |15 |
| [N-21] |Use a more recent version of Solidity |All Contracts |
| [N-22] |For functions, follow Solidity standard naming conventions (internal function style rule) |112 |
| [N-23] |Implement some type of version counter that will be incremented automatically for contract upgrades |6 |
| [N-24] |Change the way Openzeppelin codes are used in the project|18 |
| [N-25] |Floating pragma|8 |
| [N-26] |It is confusing to assign the same value to the `currentEpoch` variable |1 |

Total 26 issues


### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Project Upgrade and Stop Scenario should be |
| [S-02] |Use descriptive names for Contracts and Libraries|
| [S-03] |Use SMTChecker|

Total 3 suggestions

### [L-01] Project has NPM Dependency which uses a vulnerable version : `axios`

https://github.com/code-423n4/2023-01-ondo/blob/main/package.json#L35


### Impact

Project installs NPM packages with `package.json` file, `axios` is one of them, the project uses version 0.21.1 but this version has ReDos vulnerability.

axios is a promise-based HTTP client for the browser and Node.js

Versions of this package are vulnerable to Regular Expression Denial of Service (ReDoS) via the trim function.



```js
package.json:
  28      "deploy": "hardhat --network ethereum deploy"
  29:   },
  30:   "dependencies": {
  35:     "axios": "0.21.1",
```

### Proof Of Concept

https://github.com/axios/axios/commit/5b457116e31db0e88fede6c428e969e87f290929

https://security.snyk.io/vuln/SNYK-JS-AXIOS-1579269



### Recommended Mitigation Steps
Upgrade axios to version 0.21.3 or higher


### [L-02]  Project has NPM Dependency which uses a vulnerable version : `node-fetch`

https://github.com/code-423n4/2023-01-ondo/blob/main/package.json#L52

### Impact

node-fetch is a light-weight module that brings window.fetch to node.js

Affected versions of this package are vulnerable to Information Exposure when fetching a remote url with Cookie, if it get a Location response header, it will follow that url and try to fetch that url with provided cookie. This can lead to forwarding secure headers to 3th party



```js
package.json:
  29    },
  30:   "dependencies": {
  52:     "node-fetch": "2.6.1",

```

### Proof Of Concept

https://security.snyk.io/vuln/SNYK-JS-NODEFETCH-2342118
https://github.com/node-fetch/node-fetch/commit/1ef4b560a17e644a02a3bfdea7631ffeee578b35


### Recommended Mitigation Steps
Upgrade `node-fetch` to version 2.6.7, 3.1.1 or higher.


### [L-03] Loss of precision due to rounding

Add scalar so roundings are negligible

[CashManager.sol#L302-L303](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L302-L303)

```solidity

contracts/cash/CashManager.sol:
   88:   // Helper constant that allows us to specify basis points in calculations
   89:   uint256 public constant BPS_DENOMINATOR = 10_000;
  301  
  302:   uint256 maxDifferenceThisEpoch = (lastSetMintExchangeRate *
  303:   exchangeRateDeltaLimit) / BPS_DENOMINATOR;

```


### [L-04] Missing Event for critical parameters init and change

**Context:**
```solidity

6 results - 6 files

contracts/cash/token/CashKYCSender.sol:
  45  
  46:   function initialize(
  47      string memory name,

contracts/cash/token/CashKYCSenderReceiver.sol:
  45  
  46:   function initialize(
  47      string memory name,

contracts/lending/tokens/cCash/CCash.sol:
  29     */
  30:   function initialize(
  31      address underlying_,

contracts/lending/tokens/cCash/CTokenCash.sol:
  33     */
  34:   function initialize(
  35      ComptrollerInterface comptroller_,

contracts/lending/tokens/cToken/CErc20.sol:
  29     */
  30:   function initialize(
  31      address underlying_,

contracts/lending/tokens/cToken/CTokenModified.sol:
  33     */
  34:   function initialize(
  35      ComptrollerInterface comptroller_,

```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit

### [L-05] Use `2Step` change architecture instead of ` setfeeRecipient ` and `setAssetRecipient`

```solidity

Critical address definitions are a sensitive and irreversible process that can render a contract useless, a two-step process to protect against typos or erroneous copy/paste

contracts/cash/CashManager.sol:
  451     */
  452:   function setFeeRecipient(
  453:     address _feeRecipient
  454:   ) external override onlyRole(MANAGER_ADMIN) {
  455:     address oldFeeRecipient = feeRecipient;
  456:     feeRecipient = _feeRecipient;
  457:     emit FeeRecipientSet(oldFeeRecipient, _feeRecipient);
  458:   }
  459: 
  460:   /**
  461:    * @notice Sets asset recipient
  462:    *
  463:    * @param _assetRecipient New asset recipient address
  464:    */
  465:   function setAssetRecipient(
  466:     address _assetRecipient
  467:   ) external override onlyRole(MANAGER_ADMIN) {
  468:     address oldAssetRecipient = assetRecipient;
  469:     assetRecipient = _assetRecipient;
  470:     emit AssetRecipientSet(oldAssetRecipient, _assetRecipient);
  471:   }
```

**Recommendation:**
Use 2-stage address change



### [L-06] Stack too deep when compiling

The project cannot be compiled due to the "stack too deep" error.

The “stack too deep” error is a limitation of the current code generator. The EVM stack only has 16 slots and that’s sometimes not enough to fit all the local variables, parameters and/or return variables. The solution is to move some of them to memory, which is more expensive but at least makes your code compile.

```js
[⠊] Compiling...
[⠒] Compiling 26 files with 0.5.17
[⠢] Compiling 7 files with 0.6.12
[⠘] Compiling 178 files with 0.8.16
[⠃] Solc 0.6.12 finished in 490.34ms
[⠰] Solc 0.5.17 finished in 1.60s
[⠑] Solc 0.8.16 finished in 5.14s
Error: 
Compiler run failed

CompilerError: Stack too deep. Try compiling with `--via-ir` (cli) or the equivalent `viaIR: true` (standard JSON) while enabling the optimizer. Otherwise, try removing local variables. When compiling inline assembly: Variable headStart is 1 slot(s) too deep inside the stack. Stack too deep. Try compiling with `--via-ir` (cli) or the equivalent `viaIR: true` (standard JSON) while enabling the optimizer. Otherwise, try removing local variables.

```

ref:https://forum.openzeppelin.com/t/stack-too-deep-when-compiling-inline-assembly/11391/6

### [L-07] There is a risk that the `setMintFee` variable is accidentally execute to 0 with `setMintFee` function

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L410-L419


The `setMintFee` function stores a fee definition that is stored in the `mintFee`state variable and is defined as 0 at the start of the contract

There is no protection against accidentally setting 0 charges in the `setMintFee` function, adding a bool check i can prevent `mintFee` from being set to 0


```solidity

contracts/cash/CashManager.sol:
  409     */
  410:   function setMintFee(
  411:     uint256 _mintFee
  412:   ) external override onlyRole(MANAGER_ADMIN) {
  413:     if (_mintFee >= BPS_DENOMINATOR) {
  414:       revert MintFeeTooLarge();
  415:     }
  416:     uint256 oldMintFee = mintFee;
  417:     mintFee = _mintFee;
  418:     emit MintFeeSet(oldMintFee, _mintFee);
  419:   }

```

### Recommended Mitigation Steps

With the `isZeroCheck` bool, both `mintFee` can be set to 0 and it can be checked


```diff
+ error ZeroFeeError();

contracts/cash/CashManager.sol:
  409     */
  410:   function setMintFee(
  411:     uint256 _mintFee,
+            bool isZeroCheck
  412:   ) external override onlyRole(MANAGER_ADMIN) {
  413:     if (_mintFee >= BPS_DENOMINATOR) {
  414:       revert MintFeeTooLarge();
  415:     }
+             if (_mintFee == 0 && !isZeroCheck) {
+	 revert ZeroFeeError()
+	}

  416:     uint256 oldMintFee = mintFee;
  417:     mintFee = _mintFee;
  418:     emit MintFeeSet(oldMintFee, _mintFee);
  419:   }


```

### [L-08] No Storage Gap for `KYCRegistryClientInitializable`

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L23

### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, 
it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots. 
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:


```js
contract Base {
    uint256 base1;
    uint256[49] __gap;
}

contract Child is Base {
    uint256 child;
}
```

Openzeppelin Storage Gaps notification:
```js
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).
```



### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract
```js
uint256[50] private __gap;
```
### [L-09] The nonReentrant modifier should occur before all other modifiers

Best practice for the re-entry issue is to have non-entrancy come first in the modifier order.

This rule is not applied in the following three functions;

```solidity
3 results

contracts/cash/CashManager.sol:
  195:   function requestMint(
  196:     uint256 collateralAmountIn
  197:   )
  198:     external
  199:     override
  200:     updateEpoch
  201:     nonReentrant
  202:     whenNotPaused
  203:     checkKYC(msg.sender)
  204:   {

contracts/cash/CashManager.sol:
  241:   function claimMint(
  242:     address user,
  243:     uint256 epochToClaim
  244:   ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) {

contracts/cash/CashManager.sol:
  661     */
  662:   function requestRedemption(
  663:     uint256 amountCashToRedeem
  664:   )
  665:     external
  666:     override
  667:     updateEpoch
  668:     nonReentrant
  669:     whenNotPaused
  670:     checkKYC(msg.sender)
  671:   {

```

### [L-10] initialize() functions can be called by anybody

`initialize()` functions can be called anybody when the contract is not initialized.


Here is a definition of `initialize()` function.

```solidity

4 results - 4 files

contracts/cash/token/CashKYCSender.sol:
  46:   function initialize(

contracts/cash/token/CashKYCSenderReceiver.sol:
  46:   function initialize(

contracts/lending/tokens/cCash/CCash.sol:
  30:   function initialize(

contracts/lending/tokens/cToken/CErc20.sol:
  30:   function initialize(


```


### Recommended Mitigation Steps

Add a control that makes `initialize()` only call the Deployer Contract or EOA;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```

### [L-11] Use `uint256` instead `uint`

Project use uint and uint256
 
Number of uses:
uint  = 336 results - 10 files
uint256 = 305 results - 25 files


Some developers prefer to use `uint256` because it is consistent with other uint data types, which also specify their size, and also because making the size of the data explicit reminds the developer and the reader how much data they've got to play with, which may help prevent or detect bugs.

For example if doing ```bytes4(keccak('transfer(address, uint)’))```, you'll get a different method sig ID than ```bytes4(keccak('transfer(address, uint256)’))``` and smart contracts will only understand the latter when comparing method sig IDs



### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is ~80%. Testing all functions is best practice in terms of security criteria.


Due to its capacity, test coverage is expected to be 100%.

### [N-02] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-03] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-04] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

contracts/cash/CashManager.sol:
  409     */
  410:   function setMintFee(
  411:     uint256 _mintFee
  412:   ) external override onlyRole(MANAGER_ADMIN) {
  413:     if (_mintFee >= BPS_DENOMINATOR) {
  414:       revert MintFeeTooLarge();
  415:     }
  416:     uint256 oldMintFee = mintFee;
  417:     mintFee = _mintFee;
  418:     emit MintFeeSet(oldMintFee, _mintFee);
  419:   }
```


### [N-05] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```
### [N-06] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity
14 results - 8 files

contracts/cash/CashManager.sol:
  122:   bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
  123:   bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
  124:   bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

contracts/cash/factory/CashFactory.sol:
  43  contract CashFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

contracts/cash/factory/CashKYCSenderFactory.sol:
  43  contract CashKYCSenderFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:
  43  contract CashKYCSenderReceiverFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");


contracts/cash/kyc/KYCRegistry.sol:
  31    bytes32 public constant _APPROVAL_TYPEHASH =
  32:     keccak256(
  33        "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"

  36:   bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN"); 

contracts/cash/token/Cash.sol:
  22:   bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

contracts/cash/token/CashKYCSender.sol:
  26    bytes32 public constant KYC_CONFIGURER_ROLE =
  27:     keccak256("KYC_CONFIGURER_ROLE");

contracts/cash/token/CashKYCSenderReceiver.sol:
  26    bytes32 public constant KYC_CONFIGURER_ROLE =
  27:     keccak256("KYC_CONFIGURER_ROLE");

```

### [N-07] Mark visibility of initialize(...) functions as ``external``

```solidity
4 results
contracts/cash/token/CashKYCSender.sol:
  45  
  46:   function initialize(
  47:     string memory name,
  48:     string memory symbol,
  49:     address kycRegistry,
  50:     uint256 kycRequirementGroup
  51:   ) public initializer {

contracts/cash/token/CashKYCSenderReceiver.sol:
  45  
  46:   function initialize(
  47:     string memory name,
  48:     string memory symbol,
  49:     address kycRegistry,
  50:     uint256 kycRequirementGroup
  51:   ) public initializer {

contracts/lending/tokens/cCash/CCash.sol:
  29     */
  30:   function initialize(
  31:     address underlying_,
  32:     ComptrollerInterface comptroller_,
  33:     InterestRateModel interestRateModel_,
  34:     uint initialExchangeRateMantissa_,
  35:     string memory name_,
  36:     string memory symbol_,
  37:     uint8 decimals_,
  38:     address kycRegistry_,
  39:     uint kycRequirementGroup_
  40:   ) public {

contracts/lending/tokens/cToken/CErc20.sol:
  29     */
  30:   function initialize(
  31:     address underlying_,
  32:     ComptrollerInterface comptroller_,
  33:     InterestRateModel interestRateModel_,
  34:     uint initialExchangeRateMantissa_,
  35:     string memory name_,
  36:     string memory symbol_,
  37:     uint8 decimals_,
  38:     address kycRegistry_,
  39:     uint kycRequirementGroup_
  40:   ) public {


```

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750
### [N-08] Use underscores for number literals

```diff
2 results - 2 files

contracts/lending/JumpRateModelV2.sol:
-  29:   uint public constant blocksPerYear = 2628000;
+  29:   uint public constant blocksPerYear = 2_628_000;

contracts/lending/OndoPriceOracleV2.sol:
- 77:   uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
+ 77:   uint256 public maxChainlinkOracleTimeDelay = 90_000; // 25 hours

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-09] Lack of event emission after critical `initialize()` function

To record the init parameters for off-chain monitoring and transparency reasons, please consider emitting an event after the `initialize()` function:

```solidity
4 results
contracts/cash/token/CashKYCSender.sol:
  45  
  46:   function initialize(
  47:     string memory name,
  48:     string memory symbol,
  49:     address kycRegistry,
  50:     uint256 kycRequirementGroup
  51:   ) public initializer {

contracts/cash/token/CashKYCSenderReceiver.sol:
  45  
  46:   function initialize(
  47:     string memory name,
  48:     string memory symbol,
  49:     address kycRegistry,
  50:     uint256 kycRequirementGroup
  51:   ) public initializer {

contracts/lending/tokens/cCash/CCash.sol:
  29     */
  30:   function initialize(
  31:     address underlying_,
  32:     ComptrollerInterface comptroller_,
  33:     InterestRateModel interestRateModel_,
  34:     uint initialExchangeRateMantissa_,
  35:     string memory name_,
  36:     string memory symbol_,
  37:     uint8 decimals_,
  38:     address kycRegistry_,
  39:     uint kycRequirementGroup_
  40:   ) public {

contracts/lending/tokens/cToken/CErc20.sol:
  29     */
  30:   function initialize(
  31:     address underlying_,
  32:     ComptrollerInterface comptroller_,
  33:     InterestRateModel interestRateModel_,
  34:     uint initialExchangeRateMantissa_,
  35:     string memory name_,
  36:     string memory symbol_,
  37:     uint8 decimals_,
  38:     address kycRegistry_,
  39:     uint kycRequirementGroup_
  40:   ) public {


```
### [N-10] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```solidity

3 results - 3 files

contracts/cash/Proxy.sol:
  24      bytes memory _data
  25:   ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
  26  }

contracts/lending/tokens/cCash/CCashDelegate.sol:
  14     */
  15:   constructor() {}
  16  

contracts/lending/tokens/cToken/CTokenDelegate.sol:
  14     */
  15:   constructor() {}
  16 
```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### [N-11] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

### [N-12] Assembly Codes Specific – Should Have Comments

Since this is a low level language that is more difficult to parse by readers, include extensive documentation, comments on the rationale behind its use, clearly explaining what each assembly instruction does


This will make it easier for users to trust the code, for reviewers to validate the code, and for developers to build on or update the code.

Note that using Aseembly removes several important security features of Solidity, which can make the code more insecure and more error-prone.

```solidity
7 results - 3 files

contracts/lending/tokens/cErc20ModifiedDelegator.sol:
  1204      (bool success, bytes memory returnData) = callee.delegatecall(data);
  1205:     assembly {
  1206        if eq(success, 0) {

  1237      );
  1238:     assembly {
  1239        if eq(success, 0) {

  1258  
  1259:     assembly {
  1260        let free_mem_ptr := mload(0x40)

contracts/lending/tokens/cCash/CCash.sol:
  203      bool success;
  204:     assembly {
  205        switch returndatasize()

  243      bool success;
  244:     assembly {
  245        switch returndatasize()

contracts/lending/tokens/cToken/CErc20.sol:
  203      bool success;
  204:     assembly {
  205        switch returndatasize()

  243      bool success;
  244:     assembly {
  245        switch returndatasize()
```

### [N-13] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
34 results - 12 files

contracts/cash/CashManager.sol:
   87  
   88:   // Helper constant that allows us to specify basis points in calculations
   89:   uint256 public constant BPS_DENOMINATOR = 10_000;
   90  

  121    /// @dev Role Based Access control members
  122:   bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
  123:   bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
  124:   bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
  125  

contracts/cash/factory/CashFactory.sol:
  43  contract CashFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
  46:   bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
  47  

contracts/cash/factory/CashKYCSenderFactory.sol:
  43  contract CashKYCSenderFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
  46:   bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
  47  

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:
  43  contract CashKYCSenderReceiverFactory is IMulticall {
  44:   bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
  45:   bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
  46:   bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
  47  

contracts/cash/kyc/KYCRegistry.sol:
  30  contract KYCRegistry is AccessControlEnumerable, IKYCRegistry, EIP712 {
  31:   bytes32 public constant _APPROVAL_TYPEHASH =
  32      keccak256(

  35    // Admin role that has permission to add/remove KYC related roles
  36:   bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
  37  

contracts/cash/token/Cash.sol:
  21  contract Cash is ERC20PresetMinterPauserUpgradeable {
  22:   bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
  23  

contracts/cash/token/CashKYCSender.sol:
  25  {
  26:   bytes32 public constant KYC_CONFIGURER_ROLE =
  27      keccak256("KYC_CONFIGURER_ROLE");

contracts/cash/token/CashKYCSenderReceiver.sol:
  25  {
  26:   bytes32 public constant KYC_CONFIGURER_ROLE =
  27      keccak256("KYC_CONFIGURER_ROLE");

contracts/lending/JumpRateModelV2.sol:
  28     */
  29:   uint public constant blocksPerYear = 2628000;
  30  

contracts/lending/tokens/cErc20ModifiedDelegator.sol:
  207  
  208:   uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
  209  

  212     */
  213:   uint256 internal constant reserveFactorMaxMantissa = 1e18;
  214  

  349     */
  350:   ISanctionsList public constant sanctionsList =
  351      ISanctionsList(0x40C57923924B5c5c5455c48D93317139ADDaC8fb);

  367     */
  368:   bool public constant isCToken = true;
  369  

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol:
   34    // Maximum borrow rate that can ever be applied (.0005% / block)
   35:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   36  
   37    // Maximum fraction of interest that can be set aside for reserves
   38:   uint internal constant reserveFactorMaxMantissa = 1e18;
   39  

  114     */
  115:   uint public constant protocolSeizeShareMantissa = 0; //0%
  116  }

  165     */
  166:   ISanctionsList public constant sanctionsList =
  167      ISanctionsList(0x40C57923924B5c5c5455c48D93317139ADDaC8fb);

  183     */
  184:   bool public constant isCToken = true;
  185  

contracts/lending/tokens/cToken/CTokenInterfacesModified.sol:
   32    // Maximum borrow rate that can ever be applied (.0005% / block)
   33:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   34  
   35    // Maximum fraction of interest that can be set aside for reserves
   36:   uint internal constant reserveFactorMaxMantissa = 1e18;
   37  

  112     */
  113:   uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
  114  }

  163     */
  164:   ISanctionsList public constant sanctionsList =
  165      ISanctionsList(0x40C57923924B5c5c5455c48D93317139ADDaC8fb);

  181     */
  182:   bool public constant isCToken = true;
  183 

```

### [N-14] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.


```solidity
49 results - 2 files

contracts/cash/CashManager.sol:
  142:       revert CollateralZeroAddress();
  145:       revert CashZeroAddress();
  148:       revert AssetRecipientZeroAddress();
  151:       revert AssetSenderZeroAddress();
  154:       revert FeeRecipientZeroAddress();
  206:       revert MintRequestAmountTooSmall();
  247:       revert NoCashToClaim();
  250:       revert ExchangeRateNotSet();
  286:       revert ZeroExchangeRate();
  289:       revert EpochNotElapsed();
  292:       revert EpochExchangeRateAlreadySet();
  343:       revert UnexpectedMintBalance();
  346:       revert CannotServiceFutureEpoch();
  372:       revert MustServicePastEpoch();
  414:       revert MintFeeTooLarge();
  437:       revert MinimumDepositAmountTooSmall();
  627:       revert MintExceedsRateLimit();
  643:       revert RedeemAmountCannotBeZero();
  646:       revert RedeemExceedsRateLimit();
  673:       revert WithdrawRequestAmountTooSmall();
  717:       revert MustServicePastEpoch();
  759:         revert CollateralRedemptionTooSmall();
  857:       revert CannotServiceFutureEpoch();
  921:       revert KYCCheckFailed();

contracts/lending/tokens/cToken/CTokenModified.sol:
   109:       revert TransferNotAllowed();
   503:       revert MintFreshnessCheck();
   625:       revert RedeemFreshnessCheck();
   630:       revert RedeemTransferOutNotPossible();
   695:       revert BorrowFreshnessCheck();
   700:       revert BorrowCashNotAvailable();
   789:       revert RepayBorrowFreshnessCheck();
   890:       revert LiquidateFreshnessCheck();
   895:       revert LiquidateCollateralFreshnessCheck();
   900:       revert LiquidateLiquidatorIsBorrower();
   905:       revert LiquidateCloseAmountIsZero();
   910:       revert LiquidateCloseAmountIsUintMax();
  1017:       revert LiquidateSeizeLiquidatorIsBorrower();
  1068:       revert SetPendingAdminOwnerCheck();
  1091:       revert AcceptAdminPendingAdminCheck();
  1120:       revert SetComptrollerOwnerCheck();
  1159:       revert SetReserveFactorAdminCheck();
  1164:       revert SetReserveFactorFreshCheck();
  1169:       revert SetReserveFactorBoundsCheck();
  1262:       revert ReduceReservesAdminCheck();
  1267:       revert ReduceReservesFreshCheck();
  1272:       revert ReduceReservesCashNotAvailable();
  1277:       revert ReduceReservesCashValidation();
  1325:       revert SetInterestRateModelOwnerCheck();
  1330:       revert SetInterestRateModelFreshCheck();


```

### [N-15] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code


```solidity
6 results - 2 files

contracts/cash/CashManager.sol:
  141:     if (_collateral == address(0)) {
  144:     if (_cash == address(0)) {
  147:     if (_assetRecipient == address(0)) {
  150:     if (_assetSender == address(0)) {
  153:     if (_feeRecipient == address(0)) {

contracts/cash/kyc/KYCRegistryClient.sol:
  40:     if (_kycRegistry == address(0)) {

```

## [N-16] Avoid _shadowing_ `inherited state variables`

```solidity
contracts/cash/token/CashKYCSender.sol:
  45  
  46:   function initialize(
  47:     string memory name,
  48:     string memory symbol,
  49:     address kycRegistry,
  50:     uint256 kycRequirementGroup
  51:   ) public initializer {
  52:     __ERC20PresetMinterPauser_init(name, symbol);
  53:     __KYCRegistryClientInitializable_init(kycRegistry, kycRequirementGroup);
  54:   }


contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  85     */
  86:   function symbol() public view virtual override returns (string memory) {
  87:     return _symbol;
  88:   }

```

**Description:**
In ``` contracts/cash/token/CashKYCSender.sol #L52``` , there is a local variable named ```name``` and `symbol`, but there are a state varible named ```name``` and `symbol` in the inherited ``` ERC20Upgradeable.sol ``` with the same name. 
This use causes compilers to issue warnings, negatively affecting checking and code readability. 


```js
contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  77     */
  78:   function name() public view virtual override returns (string memory) {
  79:     return _name;
  80:   }


contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  85     */
  86:   function symbol() public view virtual override returns (string memory) {
  87:     return _symbol;
  88:   }

```

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

There is no danger here, but definitions with the same name are dangerous, especially with upgradeable contracts, which may cause problems in future versions.
The way to avoid this; Adding the _ sign to the beginning of local variable names


### [N-17] Use a more recent version of OpenZeppelin dependencies

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest OZ version.


```js
contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/IERC20Upgradeable.sol:
  1  // SPDX-License-Identifier: MIT
  2: // OpenZeppelin Contracts (last updated v4.6.0) (token/ERC20/IERC20.sol)
```



**Recommendation:**
Old version of OZ is used `(4.6.0)`, newer version can be used `(4.7.3)` 

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.7.3

### [N-18] Use `require` instead of `assert`

**Context:**

```solidity
3 results - 3 files

contracts/cash/factory/CashFactory.sol:
  97:     assert(cashProxyAdmin.owner() == guardian);

contracts/cash/factory/CashKYCSenderFactory.sol:
  106:     assert(cashKYCSenderProxyAdmin.owner() == guardian);

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:
  106:     assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);

```
**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.
This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-19] For modern and more readable code; update import usages

**Context:**
All contracts

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```
### [N-20] Compliance with Solidity Style rules in Constant expressions

**Context:**
```solidity
15 results - 4 files

contracts/lending/JumpRateModelV2.sol:
  29:   uint public constant blocksPerYear = 2628000;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:
  208:   uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
  213:   uint256 internal constant reserveFactorMaxMantissa = 1e18;
  350:   ISanctionsList public constant sanctionsList =
  368:   bool public constant isCToken = true;

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol:
   35:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   38:   uint internal constant reserveFactorMaxMantissa = 1e18;
  115:   uint public constant protocolSeizeShareMantissa = 0; //0%
  166:   ISanctionsList public constant sanctionsList =
  184:   bool public constant isCToken = true;

contracts/lending/tokens/cToken/CTokenInterfacesModified.sol:
   33:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   36:   uint internal constant reserveFactorMaxMantissa = 1e18;
  113:   uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
  164:   ISanctionsList public constant sanctionsList =
  182:   bool public constant isCToken = true;

```

**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.


### [N-21] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(0.8.16)`, newer version can be used `(0.8.17)` 

### [N-22] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity

112 results - 10 files

contracts/cash/factory/CashFactory.sol:
  48:   address internal immutable guardian;

contracts/cash/factory/CashKYCSenderFactory.sol:
  48:   address internal immutable guardian;

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:
  48:   address internal immutable guardian;

contracts/lending/JumpRateModelV2.sol:
  171:   function updateJumpRateModelInternal(
  176:   ) internal {

contracts/lending/tokens/cErc20ModifiedDelegator.sol:
  208:   uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
  213:   uint256 internal constant reserveFactorMaxMantissa = 1e18;
  238:   uint256 internal initialExchangeRateMantissa;
  273:   mapping(address => uint256) internal accountTokens;
  278:   mapping(address => mapping(address => uint256)) internal transferAllowances;
  293:   mapping(address => BorrowSnapshot) internal accountBorrows;

contracts/lending/tokens/cCash/CTokenCash.sol:
   314:   function borrowBalanceStoredInternal(
   316:   ) internal view returns (uint) {
   349:     return exchangeRateStoredInternal();
   357:   function exchangeRateStoredInternal() internal view virtual returns (uint) {
   479:   function mintInternal(uint mintAmount) internal nonReentrant {
   491:   function mintFresh(address minter, uint mintAmount) internal {
   506:     Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
   552:   function redeemInternal(uint redeemTokens) internal nonReentrant {
   563:   function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
   580:   ) internal {
   590:     Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
   669:   function borrowInternal(uint borrowAmount) internal nonReentrant {
   679:   function borrowFresh(address payable borrower, uint borrowAmount) internal {
   708:     uint accountBorrowsPrev = borrowBalanceStoredInternal(borrower);
   740:   function repayBorrowInternal(uint repayAmount) internal nonReentrant {
   751:   function repayBorrowBehalfInternal(
   754:   ) internal nonReentrant {
   771:   ) internal returns (uint) {
   793:     uint accountBorrowsPrev = borrowBalanceStoredInternal(borrower);
   845:   function liquidateBorrowInternal(
   849:   ) internal nonReentrant {
   875:   ) internal {
   942:     // If this is also the collateral, run seizeInternal to avoid re-entrancy, otherwise make an external call
   944:       seizeInternal(address(this), liquidator, borrower, seizeTokens);
   976:     seizeInternal(msg.sender, liquidator, borrower, seizeTokens);
   990:   function seizeInternal(
   995:   ) internal {
  1027:     Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
  1153:   ) internal returns (uint) {
  1411:   function getCashPrior() internal view virtual returns (uint);
  1420:   ) internal virtual returns (uint);
  1427:   function doTransferOut(address payable to, uint amount) internal virtual;

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol:
   35:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   38:   uint internal constant reserveFactorMaxMantissa = 1e18;
   61:   uint internal initialExchangeRateMantissa;
   94:   mapping(address => uint) internal accountTokens;
   97:   mapping(address => mapping(address => uint)) internal transferAllowances;
  110:   mapping(address => BorrowSnapshot) internal accountBorrows;

contracts/lending/tokens/cToken/CErc20.sol:
   67:     mintInternal(mintAmount);
   78:     redeemInternal(redeemTokens);
   91:     redeemUnderlyingInternal(redeemAmount);
  101:     borrowInternal(borrowAmount);
  111:     repayBorrowInternal(repayAmount);
  125:     repayBorrowBehalfInternal(borrower, repayAmount);
  142:     liquidateBorrowInternal(borrower, repayAmount, cTokenCollateral);

  179:   function getCashPrior() internal view virtual override returns (uint) {
  196:   ) internal virtual override returns (uint) {
  239:   ) internal virtual override {

contracts/lending/tokens/cToken/CTokenInterfacesModified.sol:
   33:   uint internal constant borrowRateMaxMantissa = 0.0005e16;
   36:   uint internal constant reserveFactorMaxMantissa = 1e18;
   59:   uint internal initialExchangeRateMantissa;
   92:   mapping(address => uint) internal accountTokens;
   95:   mapping(address => mapping(address => uint)) internal transferAllowances;
  108:   mapping(address => BorrowSnapshot) internal accountBorrows;

contracts/lending/tokens/cToken/CTokenModified.sol:
    83:    * @dev Called by both `transfer` and `transferFrom` internally
    95:   ) internal returns (uint) {
   237:       borrowBalanceStoredInternal(account),
   238:       exchangeRateStoredInternal()
   246:   function getBlockNumber() internal view virtual returns (uint) {
   306:     return borrowBalanceStoredInternal(account);
   314:   function borrowBalanceStoredInternal(
   316:   ) internal view returns (uint) {
   349:     return exchangeRateStoredInternal();
   357:   function exchangeRateStoredInternal() internal view virtual returns (uint) {
   479:   function mintInternal(uint mintAmount) internal nonReentrant {
   491:   function mintFresh(address minter, uint mintAmount) internal {
   506:     Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
   552:   function redeemInternal(uint redeemTokens) internal nonReentrant {
   563:   function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
   590:     Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
   669:   function borrowInternal(uint borrowAmount) internal nonReentrant {
   679:   function borrowFresh(address payable borrower, uint borrowAmount) internal {
   740:   function repayBorrowInternal(uint repayAmount) internal nonReentrant {
   751:   function repayBorrowBehalfInternal(
   754:   ) internal nonReentrant {
   771:   ) internal returns (uint) {
   793:     uint accountBorrowsPrev = borrowBalanceStoredInternal(borrower);
   845:   function liquidateBorrowInternal(
   849:   ) internal nonReentrant {
   875:   ) internal {
  1423:   ) internal virtual returns (uint);
  1430:   function doTransferOut(address payable to, uint amount) internal virtual;

```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-23] Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of  Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

```js
6 results - 6 files

contracts/cash/token/CashKYCSender.sol:
  45  
  46:   function initialize(
  47      string memory name,

contracts/cash/token/CashKYCSenderReceiver.sol:
  45  
  46:   function initialize(
  47      string memory name,

contracts/lending/tokens/cCash/CCash.sol:
  29     */
  30:   function initialize(
  31      address underlying_,

contracts/lending/tokens/cCash/CTokenCash.sol:
  33     */
  34:   function initialize(
  35      ComptrollerInterface comptroller_,

contracts/lending/tokens/cToken/CErc20.sol:
  29     */
  30:   function initialize(
  31      address underlying_,

contracts/lending/tokens/cToken/CTokenModified.sol:
  33     */
  34:   function initialize(
  35      ComptrollerInterface comptroller_,

```
I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js

uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address newImplementation) internal override onlyAdmin {
	authorizeUpgradeCounter+=1;
}


    }
```

### [N-24] Change the way Openzeppelin codes are used in the project

The codebase makes heavy use of OpenZeppelin's conventions, but uses the codes manually rather than @imports them, which can cause a problem when copy-pasting

```solidity
18 results - 11 files

contracts/cash/CashManager.sol:
  20  import "contracts/cash/kyc/KYCRegistryClientConstructable.sol";
  21: import "contracts/cash/external/openzeppelin/contracts/security/Pausable.sol";
  22: import "contracts/cash/external/openzeppelin/contracts/token/IERC20.sol";
  23: import "contracts/cash/external/openzeppelin/contracts/token/IERC20Metadata.sol";
  24: import "contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol";
  25: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
  26: import "contracts/cash/external/openzeppelin/contracts/security/ReentrancyGuard.sol";
  27  

contracts/cash/Proxy.sol:
  17  
  18: import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";
  19  

contracts/cash/factory/CashFactory.sol:
  18  // Proxy admin contract used in OZ upgrades plugin
  19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
  20  import "contracts/cash/Proxy.sol";

contracts/cash/factory/CashKYCSenderFactory.sol:
  18  // Proxy admin contract used in OZ upgrades plugin
  19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
  20  import "contracts/cash/Proxy.sol";

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:
  18  // Proxy admin contract used in OZ upgrades plugin
  19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
  20  import "contracts/cash/Proxy.sol";

contracts/cash/kyc/KYCRegistry.sol:
  18  import "contracts/cash/interfaces/IKYCRegistry.sol";
  19: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
  20  import "contracts/cash/external/chainalysis/ISanctionsList.sol";
  21: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/EIP712.sol";
  22: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol";
  23  

contracts/cash/kyc/KYCRegistryClientInitializable.sol:
  17  import "contracts/cash/kyc/KYCRegistryClient.sol";
  18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";
  19  

contracts/cash/token/Cash.sol:
  17  
  18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
  19  

contracts/cash/token/CashKYCSender.sol:
  17  
  18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
  19  import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

contracts/cash/token/CashKYCSenderReceiver.sol:
  17  
  18: import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
  19  import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

contracts/lending/OndoPriceOracleV2.sol:
  17  import "./IOndoPriceOracleV2.sol";
  18: import "contracts/cash/external/openzeppelin/contracts/access/Ownable.sol";
  19  import "contracts/lending/chainlink/AggregatorV3Interface.sol";

```
### [N-25] Floating pragma

**Description:**
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
https://swcregistry.io/docs/SWC-103

Floating Pragma List: 
```solidity

8 results - 8 files

contracts/lending/tokens/cCash/CCash.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CCashDelegate.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CTokenCash.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CErc20.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenDelegate.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenInterfacesModified.sol:
  2: pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenModified.sol:
  2: pragma solidity ^0.8.10;
```


**Recommendation:**
Lock the pragma version and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.


### [N-26] It is confusing to assign the same value to the `currentEpoch` variable

The `currentEpoch` variable is also set with the same value in the constructor, which means redefining the same value and confusing

If the same value is incorrectly set instead of another value, this Medium finding into an error class


```solidity
contracts/cash/CashManager.sol:
  125  
  126:   /// @notice constructor
  127:   constructor(
  128:     address _collateral,
  129:     address _cash,
  130:     address managerAdmin,
  131:     address pauser,
  132:     address _assetRecipient,
  133:     address _assetSender,
  134:     address _feeRecipient,
  135:     uint256 _mintLimit,
  136:     uint256 _redeemLimit,
  137:     uint256 _epochDuration,
  138:     address _kycRegistry,
  139:     uint256 _kycRequirementGroup
  140:   ) KYCRegistryClientConstructable(_kycRegistry, _kycRequirementGroup) {

  168:     currentEpoch = currentEpoch; //???

  183:   }

```



### [S-01] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [S-02] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

### [S-03] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

