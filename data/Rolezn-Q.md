 ## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Init functions are susceptible to front-running | 2 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Loss of precision due to rounding | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Missing parameter validation in `constructor` | 9 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Missing Contract-existence Checks Before Low-level Calls | 4 |
| [LOW&#x2011;5](#LOW&#x2011;5) | The `nonReentrant` modifier should occur before all other modifiers | 4 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Contracts are not using their OZ Upgradeable counterparts | 14 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Prevent division by 0 | 1 |
| [LOW&#x2011;8](#LOW&#x2011;8) | `require()` should be used instead of `assert()` | 3 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Use `safetransfer` Instead Of `transfer`  | 6 |
| [LOW&#x2011;10](#LOW&#x2011;10) | Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project | 10 |
| [LOW&#x2011;11](#LOW&#x2011;11) | TransferOwnership Should Be Two Step | 3 |
| [LOW&#x2011;12](#LOW&#x2011;12) | No Storage Gap For Upgradeable Contracts | 4 |
| [LOW&#x2011;13](#LOW&#x2011;13) | Use `safeTransferOwnership` instead of `transferOwnership` function | 3 |

Total: 66 contexts over 13 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 36 |
| [NC&#x2011;2](#NC&#x2011;2) | Avoid Floating Pragmas: The Version Should Be Locked | 11 |
| [NC&#x2011;3](#NC&#x2011;3) | Compliance with Solidity Style rules in Constant expressions | 15 |
| [NC&#x2011;4](#NC&#x2011;4) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 14 |
| [NC&#x2011;5](#NC&#x2011;5) | Critical Changes Should Use Two-step Procedure | 36 |
| [NC&#x2011;6](#NC&#x2011;6) | Duplicate imports | 2 |
| [NC&#x2011;7](#NC&#x2011;7) | Function writing that does not comply with the Solidity Style Guide  | 31 |
| [NC&#x2011;8](#NC&#x2011;8) | Use `delete` to Clear Variables | 5 |
| [NC&#x2011;9](#NC&#x2011;9) | NatSpec return parameters should be included in contracts | 1 |
| [NC&#x2011;10](#NC&#x2011;10) | No need to initialize uints to zero | 2 |
| [NC&#x2011;11](#NC&#x2011;11) | Implementation contract may not be initialized | 14 |
| [NC&#x2011;12](#NC&#x2011;12) | NatSpec comments should be increased in contracts | 1 |
| [NC&#x2011;13](#NC&#x2011;13) | Non-usage of specific imports | 20 |
| [NC&#x2011;14](#NC&#x2011;14) | Use a more recent version of Solidity | 31 |
| [NC&#x2011;15](#NC&#x2011;15) | Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant | 1 |
| [NC&#x2011;16](#NC&#x2011;16) | Use of Block.Timestamp | 2 |

Total: 222 contexts over 16 issues

## Low Risk Issues



### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Init functions are susceptible to front-running

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: code-423n4/2021-09-sushimiso-findings#64)

#### <ins>Proof Of Concept</ins>


```solidity
function __KYCRegistryClientInitializable_init(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L44

```solidity
function __KYCRegistryClientInitializable_init_unchained(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L58



#### <ins>Recommended Mitigation Steps</ins>

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enforce prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Loss of precision due to rounding

Add scalars so roundings are negligible

#### <ins>Proof Of Concept</ins>


```solidity
492: cashAmountOut = amountE24 / epochToExchangeRate[epoch];

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L492


```solidity
331: return principalTimesIndex / borrowSnapshot.interestIndex;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L331

```solidity
331: return principalTimesIndex / borrowSnapshot.interestIndex;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L331






### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
uint256 _mintLimit,
uint256 _redeemLimit,
uint256 _epochDuration,
uint256 _kycRequirementGroup
```


```solidity
uint baseRatePerYear,
uint multiplierPerYear,
uint jumpMultiplierPerYear,
uint kink_,
address owner_

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L60-L64


#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```solidity
962: (bool success, bytes memory ret) = address(exCallData[i].target).call{
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L962

```solidity
128: (bool success, bytes memory ret) = address(exCallData[i].target).call{
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L128

```solidity
138: (bool success, bytes memory ret) = address(exCallData[i].target).call{
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L138

```solidity
138: (bool success, bytes memory ret) = address(exCallData[i].target).call{
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L138





#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```solidity
453: function setFeeRecipient: address _feeRecipient
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L453

```solidity
466: function setAssetRecipient: address _assetRecipient
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L466

```solidity
804: function setAssetSender: address newAssetSender
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L804

```solidity
80: function setPrice: address fToken
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L80

```solidity
93: function setFTokenToCToken: address fToken
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L93

```solidity
131: function setPriceCap: address fToken
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L131

```solidity
163: function setPrice: address fToken
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L163

```solidity
195: function setFTokenToCToken: address fToken
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L195

```solidity
721: function _setImplementation: address implementation_
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L721

```solidity
31: function initialize: address underlying_
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L31

```solidity
31: function initialize: address underlying_
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L31



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```solidity
195: function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L195

```solidity
241: function claimMint(
    address user,
    uint256 epochToClaim
  ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L241

```solidity
662: function requestRedemption(
    uint256 amountCashToRedeem
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L662

```solidity
949: function multiexcall(
    ExCallData[] calldata exCallData
  )
    external
    payable
    override
    nonReentrant
    onlyRole(MANAGER_ADMIN)
    whenPaused
    returns (bytes[] memory results)
  {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L949



#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.




### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
21: import "contracts/cash/external/openzeppelin/contracts/security/Pausable.sol";
22: import "contracts/cash/external/openzeppelin/contracts/token/IERC20.sol";
23: import "contracts/cash/external/openzeppelin/contracts/token/IERC20Metadata.sol";
24: import "contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol";
25: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
26: import "contracts/cash/external/openzeppelin/contracts/security/ReentrancyGuard.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L21-L26

```solidity
18: import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/Proxy.sol#L18

```solidity
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L19

```solidity
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L19

```solidity
19: import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L19

```solidity
19: import "contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol";
21: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/EIP712.sol";
22: import "contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L19-L22

```solidity
18: import "contracts/cash/external/openzeppelin/contracts/access/Ownable.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L18



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.




### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Prevent division by 0

On several locations in the code precautions are not being taken for not dividing by 0, this will revert the code.

These functions can be called with 0 value in the input, this value is not checked for being bigger than 0, that means in some scenarios this can potentially trigger a division by zero.

#### <ins>Proof Of Concept</ins>

Should `quantityBurned` be equal to value 0 then this will cause the code to revert.
This can happen if `redemptionInfoPerEpoch[epochToService].totalBurned` is equal to `refundedAmt`.

```solidity
755: uint256 collateralAmountDue = (amountToDist * cashAmountReturned) /
        quantityBurned
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L755



#### <ins>Recommended Mitigation Steps</ins>

Recommend making sure division by 0 won’t occur by checking the variables beforehand and handling this edge case.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
97: assert(cashProxyAdmin.owner() == guardian);

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L97

```solidity
106: assert(cashKYCSenderProxyAdmin.owner() == guardian);

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106

```solidity
106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106





### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Use `safetransfer` Instead Of `transfer`

It is good to add a `require()` statement that checks the return value of token transfers or to use something like OpenZeppelin’s `safeTransfer`/`safeTransferFrom` unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

For example, Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert.

#### <ins>Proof Of Concept</ins>


```solidity
160: token.transfer(admin, balance);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L160

```solidity
201: token.transferFrom(from, address(this), amount);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L201

```solidity
241: token.transfer(to, amount);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L241

```solidity
160: token.transfer(admin, balance);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L160

```solidity
201: token.transferFrom(from, address(this), amount);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L201

```solidity
241: token.transfer(to, amount);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L241



#### <ins>Recommended Mitigation Steps</ins>

Consider using `safeTransfer`/`safeTransferFrom` or `require()` consistently.



### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Admin privilege - A single point of failure can allow a hacked or malicious owner use critical functions in the project

The `owner` role has a single point of failure and `onlyOwner` can use critical a few functions.

`owner` role in the project:
Owner is not behind a multisig and changes are not behind a timelock.

Even if protocol admins/developers are not malicious there is still a chance for Owner keys to be stolen. In such a case, the attacker can cause serious damage to the project due to important functions. In such a case, users who have invested in project will suffer high financial losses.

#### Impact
Hacked owner or malicious owner can immediately use critical functions in the project.

#### <ins>Proof Of Concept</ins>

```solidity
80: function setPrice(address fToken, uint256 price) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L80

```solidity
92: function setFTokenToCToken(
    address fToken,
    address cToken
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L92

```solidity
106: function setOracle(address newOracle) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L106

```solidity
130: function setPriceCap(
    address fToken,
    uint256 value
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L130

```solidity
145: function setFTokenToOracleType(
    address fToken,
    OracleType oracleType
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L145

```solidity
163: function setPrice(address fToken, uint256 price) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L163

```solidity
182: function setOracle(address newOracle) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L182

```solidity
194: function setFTokenToCToken(
    address fToken,
    address cToken
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L194

```solidity
233: function setFTokenToChainlinkOracle(
    address fToken,
    address newChainlinkOracle
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L233

```solidity
309: function setMaxChainlinkOracleTimeDelay(
    uint256 _maxChainlinkOracleTimeDelay
  ) external override onlyOwner {

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L309



#### <ins>Recommended Mitigation Steps</ins>
Add a time lock to critical functions. Admin-only functions that change critical parameters should emit events and have timelocks.
Events allow capturing the changed parameters so that off-chain tools/interfaces can register such changes with timelocks that allow users to evaluate them and consider if they would like to engage/exit based on how they perceive the changes as affecting the trustworthiness of the protocol or profitability of the implemented financial services.

Allow only multi-signature wallets to call the function to reduce the likelihood of an attack.

https://twitter.com/danielvf/status/1572963475101556738?s=20&t=V1kvzfJlsx-D2hfnG0OmuQ

#### Reference
The status quo regarding significant centralization vectors has always been to award M severity but I have lowered it to QA as it is a common finding this is also in order to at least warn users of the protocol of this category of risks. See <a href="https://gist.github.com/GalloDaSballo/881e7a45ac14481519fb88f34fdb8837">here</a> for list of centralization issues previously judged.


### <a href="#Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> TransferOwnership Should Be Two Step

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

#### <ins>Proof Of Concept</ins>


```solidity
96: function deployCash: cashProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L96

```solidity
105: function deployCashKYCSender: cashKYCSenderProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L105

```solidity
105: function deployCashKYCSenderReceiver: cashKYCSenderReceiverProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L105



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> No storage gap for upgradeable contract might lead to storage slot collision

For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

#### <ins>Proof Of Concept</ins>

However, the contract doesn't contain a storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```solidity
TransparentUpgradeableProxy
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/Proxy.sol#L18

```solidity
ERC20PresetMinterPauserUpgradeable
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/Cash.sol#L18

```solidity
ERC20PresetMinterPauserUpgradeable
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#0

```solidity
ERC20PresetMinterPauserUpgradeable
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#0



#### <ins>Recommended Mitigation Steps</ins>
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
    uint256[50] private __gap;
```



### <a href="#Summary">[LOW&#x2011;13]</a><a name="LOW&#x2011;13"> Use `safeTransferOwnership` instead of `transferOwnership` function

Use `safeTransferOwnership` which is safer. Use it as it is more secure due to 2-stage ownership transfer.

#### <ins>Proof Of Concept</ins>


```solidity
96: function deployCash: cashProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L96

```solidity
105: function deployCashKYCSender: cashKYCSenderProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L105

```solidity
105: function deployCashKYCSenderReceiver: cashKYCSenderReceiverProxyAdmin.transferOwnership(guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L105



#### <ins>Recommended Mitigation Steps</ins>

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>

```solidity
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
}
```



## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to the following functions:

#### <ins>Proof Of Concept</ins>


```solidity
281: function setMintExchangeRate(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L281

```solidity
336: function setPendingMintBalance(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L336

```solidity
392: function setMintExchangeRateDeltaLimit(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L392

```solidity
410: function setMintFee(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L410

```solidity
433: function setMinimumDepositAmount(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L433

```solidity
452: function setFeeRecipient(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L452

```solidity
465: function setAssetRecipient(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L465

```solidity
546: function setEpochDuration(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L546

```solidity
596: function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L596

```solidity
609: function setRedeemLimit(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L609

```solidity
803: function setAssetSender(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L803

```solidity
817: function setRedeemMinimum(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L817

```solidity
851: function setPendingRedemptionBalance(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L851

```solidity
896: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L896

```solidity
907: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L907

```solidity
34: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L34

```solidity
40: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L40

```solidity
34: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L34

```solidity
40: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L40

```solidity
28: function setPrice(address fToken, uint256 price) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L28

```solidity
30: function setFTokenToCToken(address fToken, address cToken) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L30

```solidity
32: function setOracle(address newOracle) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L32

```solidity
80: function setPrice(address fToken, uint256 price) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L80

```solidity
92: function setFTokenToCToken(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L92

```solidity
106: function setOracle(address newOracle) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L106

```solidity
130: function setPriceCap(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L130

```solidity
145: function setFTokenToOracleType(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L145

```solidity
163: function setPrice(address fToken, uint256 price) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L163

```solidity
182: function setOracle(address newOracle) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L182

```solidity
194: function setFTokenToCToken(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L194

```solidity
233: function setFTokenToChainlinkOracle(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L233

```solidity
309: function setMaxChainlinkOracleTimeDelay(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L309

```solidity
1356: function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1356

```solidity
1378: function setKYCRegistry(address _kycRegistry) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1378

```solidity
1359: function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1359

```solidity
1381: function setKYCRegistry(address _kycRegistry) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1381







### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

```solidity
Found usage of floating pragmas ^0.5.16 of Solidity in [JumpRateModelV2.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L1

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [IMulticall.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/test/upgrades/IMulticall.sol#L16

```solidity
Found usage of floating pragmas ^0.5.12 of Solidity in [cErc20ModifiedDelegator.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CCash.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CCashDelegate.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CTokenCash.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CTokenInterfacesModifiedCash.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CErc20.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CTokenDelegate.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CTokenInterfacesModified.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2

```solidity
Found usage of floating pragmas ^0.8.10 of Solidity in [CTokenModified.sol]
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2






### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Compliance with Solidity Style rules in Constant expressions

#### <ins>Proof Of Concept</ins>


```solidity
29: uint public constant blocksPerYear = 2628000;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L29

```solidity
208: uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L208

```solidity
213: uint256 internal constant reserveFactorMaxMantissa = 1e18;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L213

```solidity
350: ISanctionsList public constant sanctionsList =
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L350

```solidity
368: bool public constant isCToken = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L368

```solidity
35: uint internal constant borrowRateMaxMantissa = 0.0005e16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35

```solidity
38: uint internal constant reserveFactorMaxMantissa = 1e18;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L38

```solidity
115: uint public constant protocolSeizeShareMantissa = 0; //0%
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115

```solidity
166: ISanctionsList public constant sanctionsList =
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L166

```solidity
184: bool public constant isCToken = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L184

```solidity
33: uint internal constant borrowRateMaxMantissa = 0.0005e16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33

```solidity
36: uint internal constant reserveFactorMaxMantissa = 1e18;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36

```solidity
113: uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L113

```solidity
164: ISanctionsList public constant sanctionsList =
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L164

```solidity
182: bool public constant isCToken = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L182



#### <ins>Recommended Mitigation Steps</ins>

Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```solidity
122: bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L122

```solidity
123: bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L123

```solidity
124: bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L124

```solidity
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L44

```solidity
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L45

```solidity
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L44

```solidity
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L45

```solidity
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44

```solidity
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L45

```solidity
31: bytes32 public constant _APPROVAL_TYPEHASH =
    keccak256(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L31

```solidity
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L36

```solidity
22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/Cash.sol#L22

```solidity
26: bytes32 public constant KYC_CONFIGURER_ROLE =
    keccak256("KYC_CONFIGURER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L26

```solidity
26: bytes32 public constant KYC_CONFIGURER_ROLE =
    keccak256("KYC_CONFIGURER_ROLE");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L26





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
281: function setMintExchangeRate(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L281

```solidity
336: function setPendingMintBalance(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L336

```solidity
392: function setMintExchangeRateDeltaLimit(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L392

```solidity
410: function setMintFee(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L410

```solidity
433: function setMinimumDepositAmount(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L433

```solidity
452: function setFeeRecipient(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L452

```solidity
465: function setAssetRecipient(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L465

```solidity
546: function setEpochDuration(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L546

```solidity
596: function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L596

```solidity
609: function setRedeemLimit(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L609

```solidity
803: function setAssetSender(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L803

```solidity
817: function setRedeemMinimum(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L817

```solidity
851: function setPendingRedemptionBalance(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L851

```solidity
896: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L896

```solidity
907: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L907

```solidity
34: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L34

```solidity
40: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L40

```solidity
34: function setKYCRequirementGroup(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L34

```solidity
40: function setKYCRegistry(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L40

```solidity
28: function setPrice(address fToken, uint256 price) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L28

```solidity
30: function setFTokenToCToken(address fToken, address cToken) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L30

```solidity
32: function setOracle(address newOracle) external;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L32

```solidity
80: function setPrice(address fToken, uint256 price) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L80

```solidity
92: function setFTokenToCToken(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L92

```solidity
106: function setOracle(address newOracle) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L106

```solidity
130: function setPriceCap(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L130

```solidity
145: function setFTokenToOracleType(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L145

```solidity
163: function setPrice(address fToken, uint256 price) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L163

```solidity
182: function setOracle(address newOracle) external override onlyOwner {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L182

```solidity
194: function setFTokenToCToken(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L194

```solidity
233: function setFTokenToChainlinkOracle(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L233

```solidity
309: function setMaxChainlinkOracleTimeDelay(
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L309

```solidity
1356: function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1356

```solidity
1378: function setKYCRegistry(address _kycRegistry) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1378

```solidity
1359: function setKYCRequirementGroup(uint256 _kycRequirementGroup) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1359

```solidity
1381: function setKYCRegistry(address _kycRegistry) external {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1381



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Duplicate imports

There are several occasions where there several imports of the same file

#### <ins>Proof Of Concept</ins>


```solidity
17: import "contracts/cash/kyc/KYCRegistryClient.sol";
21: import "contracts/cash/kyc/KYCRegistryClient.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L21












### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

All in-scope contracts




### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

```solidity
259: mintRequestsPerEpoch[epochToClaim][user] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L259

```solidity
580: currentRedeemAmount = 0;
581: currentMintAmount = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L580-L581

```solidity
754: redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L754

```solidity
790: redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L790





### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
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



### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
113: uint startingAllowance = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L113

```solidity
113: uint startingAllowance = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L113








### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
127: constructor(
    address _collateral,
    address _cash,
    address managerAdmin,
    address pauser,
    address _assetRecipient,
    address _assetSender,
    address _feeRecipient,
    uint256 _mintLimit,
    uint256 _redeemLimit,
    uint256 _epochDuration,
    address _kycRegistry,
    uint256 _kycRequirementGroup
  ) KYCRegistryClientConstructable(_kycRegistry, _kycRequirementGroup)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L127

```solidity
21: constructor(
    address _logic,
    address _admin,
    bytes memory _data
  ) TransparentUpgradeableProxy(_logic, _admin, _data)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/Proxy.sol#L21

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L53

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L53

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L53

```solidity
51: constructor(
    address admin,
    address _sanctionsList
  ) EIP712("OndoKYCRegistry", "1")
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L51

```solidity
36: constructor(address _kycRegistry, uint256 _kycRequirementGroup)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L36

```solidity
25: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/Cash.sol#L25

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L30

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L30

```solidity
59: constructor(
    uint baseRatePerYear,
    uint multiplierPerYear,
    uint jumpMultiplierPerYear,
    uint kink_,
    address owner_
  ) public
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L59

```solidity
673: constructor(
    address underlying_,
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint256 initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address payable admin_,
    address implementation_,
    address kycRegistry_,
    uint256 kycRequirementGroup_,
    bytes memory becomeImplementationData
  ) public
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L673

```solidity
15: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L15

```solidity
15: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L15





### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
3: import "./compound/InterestRateModel.sol";
4: import "./compound/SafeMath.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L3-L4

```solidity
17: import "./IOndoPriceOracle.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L17

```solidity
17: import "./IOndoPriceOracleV2.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L17

```solidity
4: import "./CTokenCash.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L4

```solidity
4: import "./CCash.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L4

```solidity
4: import "../cErc20Delegate/ComptrollerInterface.sol";
5: import "../cErc20Delegate/ErrorReporter.sol";
6: import "../cErc20Delegate/EIP20Interface.sol";
7: import "../cErc20Delegate/InterestRateModel.sol";
8: import "../cErc20Delegate/ExponentialNoError.sol";
9: import "./CTokenInterfacesModifiedCash.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L4-L9

```solidity
4: import "./CTokenModified.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L4

```solidity
4: import "./CErc20.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L4

```solidity
4: import "../cErc20Delegate/ComptrollerInterface.sol";
5: import "../cErc20Delegate/ErrorReporter.sol";
6: import "../cErc20Delegate/EIP20Interface.sol";
7: import "../cErc20Delegate/InterestRateModel.sol";
8: import "../cErc20Delegate/ExponentialNoError.sol";
9: import "./CTokenInterfacesModified.sol";

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L4-L9




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/Proxy.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/ICashManager.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/IKYCRegistry.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/IKYCRegistryClient.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/interfaces/IMulticall.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClient.sol#L20

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L19

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L20

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/Cash.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSender.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/token/CashKYCSenderReceiver.sol#L16

```solidity
pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracle.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/IOndoPriceOracleV2.sol#L15

```solidity
pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/JumpRateModelV2.sol#L1

```solidity
pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracle.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/OndoPriceOracleV2.sol#L15

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/test/upgrades/IMulticall.sol#L16

```solidity
pragma solidity ^0.5.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CErc20.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Adding A Return Statement When The Function Defines A Named Return Variable, Is Redundant

#### <ins>Proof Of Concept</ins>


```solidity
795: return totalCashAmountRefunded
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L795





### <a href="#Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Stop using `v != 27 && v != 28` or `v == 27 || v == 28`

See <a href="https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg">this</a> for reference

#### <ins>Proof Of Concept</ins>


```solidity
87: v == 27 || v == 28
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L87






### <a href="#Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
577: uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/CashManager.sol#L577

```solidity
92: require(block.timestamp <= deadline, "KYCRegistry: signature expired");
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash/kyc/KYCRegistry.sol#L92



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.






