
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 4 |  - |
| [G&#x2011;02] | Avoid contract existence checks by using low level calls | 2 |  200 |
| [G&#x2011;03] | State variables should be cached in stack variables rather than re-reading them from storage | 15 |  1455 |
| [G&#x2011;04] | Multiple accesses of a mapping/array should use a local variable cache | 8 |  336 |
| [G&#x2011;05] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 3 |  339 |
| [G&#x2011;06] | `internal` functions only called once can be inlined to save gas | 4 |  80 |
| [G&#x2011;07] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 4 |  340 |
| [G&#x2011;08] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 9 |  540 |
| [G&#x2011;09] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 12 |  - |
| [G&#x2011;10] | Optimize names to save gas | 8 |  176 |
| [G&#x2011;11] | Use a more recent version of solidity | 2 |  - |
| [G&#x2011;12] | Splitting `require()` statements that use `&&` saves gas | 1 |  3 |
| [G&#x2011;13] | Using `private` rather than `public` for constants, saves gas | 1 |  - |
| [G&#x2011;14] | Use custom errors rather than `revert()`/`require()` strings to save gas | 16 |  - |
| [G&#x2011;15] | Functions guaranteed to revert when called by normal users can be marked `payable` | 34 |  714 |
| [G&#x2011;16] | Don't use `_msgSender()` if not supporting EIP-2771 | 3 |  48 |

Total: 126 instances over 16 issues with **4231 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 4 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

79      mapping(uint256 => RedemptionRequests) public redemptionInfoPerEpoch;
80    
81      // Mapping used for getting the exchange rate during a given epoch
82      mapping(uint256 => uint256) public epochToExchangeRate;
83    
84      // Nested mapping containing mint requests for an epoch
85      // { <epoch> : {<user> : <collateralAmount> }
86:     mapping(uint256 => mapping(address => uint256)) public mintRequestsPerEpoch;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L79-L86

```solidity
File: contracts/lending/OndoPriceOracle.sol

45      mapping(address => uint256) public fTokenToUnderlyingPrice;
46    
47      /// @notice fToken to cToken associations for piggy backing off
48      ///         of cToken oracles
49:     mapping(address => address) public fTokenToCToken;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L45-L49

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

55      mapping(address => OracleType) public fTokenToOracleType;
56    
57      /// @notice Contract storage for fToken's underlying asset prices
58      mapping(address => uint256) public fTokenToUnderlyingPrice;
59    
60      /// @notice fToken to cToken associations for piggy backing off
61      ///         of Compound's Oracle
62:     mapping(address => address) public fTokenToCToken;

70      mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
71    
72      /// @notice Price cap for the underlying asset of an fToken. Optional.
73:     mapping(address => uint256) public fTokenToUnderlyingPriceCap;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L55-L62

### [G&#x2011;02]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 2 instances of this issue:*

```solidity
File: contracts/lending/OndoPriceOracle.sol

/// @audit underlying()
/// @audit underlying()
121:        CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L121

### [G&#x2011;03]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 15 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit feeRecipient on line 725
726:      emit RedemptionFeesCollected(feeRecipient, fees, epochToService);

/// @audit minimumRedeemAmount on line 821
822:      emit MinimumRedeemAmountSet(oldRedeemMin, minimumRedeemAmount);

/// @audit currentEpoch on line 221
225:        currentEpoch,

/// @audit currentEpoch on line 678
681:      redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;

/// @audit currentEpoch on line 681
685:      emit RedemptionRequested(msg.sender, amountCashToRedeem, currentEpoch);

/// @audit epochDuration on line 172
176:        (block.timestamp % epochDuration);

/// @audit epochDuration on line 578
585:          (block.timestamp % epochDuration);

/// @audit lastSetMintExchangeRate on line 296
297:        rateDifference = exchangeRate - lastSetMintExchangeRate;

/// @audit lastSetMintExchangeRate on line 297
298:      } else if (exchangeRate < lastSetMintExchangeRate) {

/// @audit lastSetMintExchangeRate on line 298
299:        rateDifference = lastSetMintExchangeRate - exchangeRate;

/// @audit lastSetMintExchangeRate on line 299
302:      uint256 maxDifferenceThisEpoch = (lastSetMintExchangeRate *

/// @audit lastSetMintExchangeRate on line 302
310:          lastSetMintExchangeRate,

/// @audit lastSetMintExchangeRate on line 310
314:        uint256 oldExchangeRate = lastSetMintExchangeRate;

/// @audit lastSetMintExchangeRate on line 377
383:        lastSetMintExchangeRate

/// @audit redemptionInfoPerEpoch[epoch].totalBurned on line 867
874:        redemptionInfoPerEpoch[epoch].totalBurned

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L726

### [G&#x2011;04]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There are 8 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit redemptionInfoPerEpoch[currentEpoch] on line 678
681:      redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;

/// @audit redemptionInfoPerEpoch[epochToService] on line 752
754:        redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;

/// @audit redemptionInfoPerEpoch[epochToService] on line 788
790:        redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;

/// @audit redemptionInfoPerEpoch[epoch] on line 859
865:        redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;

/// @audit redemptionInfoPerEpoch[epoch] on line 865
867:        redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;

/// @audit redemptionInfoPerEpoch[epoch] on line 867
869:      redemptionInfoPerEpoch[epoch].addressToBurnAmt[user] = balance;

/// @audit redemptionInfoPerEpoch[epoch] on line 869
874:        redemptionInfoPerEpoch[epoch].totalBurned

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L681

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit fTokenToChainlinkOracle[fToken] on line 260
264:      fTokenToChainlinkOracle[fToken].oracle = AggregatorV3Interface(

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L264

### [G&#x2011;05]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

582:        currentEpoch += epochDifference;

630:      currentMintAmount += collateralAmountIn;

649:      currentRedeemAmount += amount;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L582

### [G&#x2011;06]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 4 instances of this issue:*

```solidity
File: contracts/lending/OndoPriceOracle.sol

119:    function _setFTokenToCToken(address fToken, address cToken) internal {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L119

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

210:    function _setFTokenToCToken(address fToken, address cToken) internal {

251     function _setFTokenToChainlinkOracle(
252       address fToken,
253:      address chainlinkOracle

324:    function _min(uint256 a, uint256 b) internal pure returns (uint256) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L210

### [G&#x2011;07]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 4 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit if-condition on line 296
297:        rateDifference = exchangeRate - lastSetMintExchangeRate;

/// @audit if-condition on line 298
299:        rateDifference = lastSetMintExchangeRate - exchangeRate;

/// @audit if-condition on line 864
865:        redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;

/// @audit if-condition on line 866
867:        redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L297

### [G&#x2011;08]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 9 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

750:      for (uint256 i = 0; i < size; ++i) {

786:      for (uint256 i = 0; i < size; ++i) {

933:      for (uint256 i = 0; i < size; ++i) {

961:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L750

```solidity
File: contracts/cash/factory/CashFactory.sol

127:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L127

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L137

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

163:      for (uint256 i = 0; i < length; i++) {

180:      for (uint256 i = 0; i < length; i++) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L163

### [G&#x2011;09]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 12 instances of this issue:*

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

163       require(
164         msg.sender == guardian,
165         "CashKYCSenderFactory: You are not the Guardian"
166:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L163-L166

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

163       require(
164         msg.sender == guardian,
165         "CashKYCSenderReceiverFactory: You are not the Guardian"
166:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L163-L166

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

88        require(
89          !kycState[kycRequirementGroup][user],
90          "KYCRegistry: user already verified"
91:       );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L88-L91

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

63        require(
64          _getKYCStatus(_msgSender()),
65          "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
66:       );

70          require(
71            _getKYCStatus(from),
72            "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
73:         );

78          require(
79            _getKYCStatus(to),
80            "CashKYCSenderReceiver: `to` address must be KYC'd to receive tokens"
81:         );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L63-L66

```solidity
File: contracts/cash/token/CashKYCSender.sol

63        require(
64          _getKYCStatus(_msgSender()),
65          "CashKYCSender: must be KYC'd to initiate transfer"
66:       );

70          require(
71            _getKYCStatus(from),
72            "CashKYCSender: `from` address must be KYC'd to send tokens"
73:         );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L63-L66

```solidity
File: contracts/cash/token/Cash.sol

36        require(
37          hasRole(TRANSFER_ROLE, _msgSender()),
38          "Cash: must have TRANSFER_ROLE to transfer"
39:       );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L36-L39

```solidity
File: contracts/lending/OndoPriceOracle.sol

120       require(
121         CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
122         "cToken and fToken must have the same underlying asset if cToken nonzero"
123:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L120-L123

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

215       require(
216         CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
217         "cToken and fToken must have the same underlying asset"
218:      );

280       require(
281         fTokenToOracleType[fToken] == OracleType.CHAINLINK,
282         "fToken is not configured for Chainlink oracle"
283:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L215-L218

### [G&#x2011;10]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 8 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit setPendingMintBalance(), setEpochDuration(), transitionEpoch(), setMintLimit(), setRedeemLimit(), setAssetSender(), setRedeemMinimum(), getBurnedQuantity(), setPendingRedemptionBalance()
28:   contract CashManager is

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L28

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit deployCash()
43:   contract CashFactory is IMulticall {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L43

```solidity
File: contracts/cash/interfaces/ICashManager.sol

/// @audit requestMint(), claimMint(), overrideExchangeRate(), setAssetRecipient(), setFeeRecipient(), setAssetSender(), setMinimumDepositAmount(), setMintFee(), setMintExchangeRate(), setMintExchangeRateDeltaLimit(), requestRedemption(), completeRedemptions()
17:   interface ICashManager {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/ICashManager.sol#L17

```solidity
File: contracts/cash/interfaces/IKYCRegistryClient.sol

/// @audit kycRequirementGroup(), kycRegistry(), setKYCRequirementGroup(), setKYCRegistry()
25:   interface IKYCRegistryClient {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IKYCRegistryClient.sol#L25

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit addKYCAddressViaSignature(), assignRoletoKYCGroup(), addKYCAddresses(), removeKYCAddresses()
30:   contract KYCRegistry is AccessControlEnumerable, IKYCRegistry, EIP712 {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L30

```solidity
File: contracts/lending/IOndoPriceOracle.sol

/// @audit setPrice(), setFTokenToCToken(), setOracle()
27:   interface IOndoPriceOracle is PriceOracle {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracle.sol#L27

```solidity
File: contracts/lending/IOndoPriceOracleV2.sol

/// @audit setPrice(), setFTokenToCToken(), setOracle()
27:   interface IOndoPriceOracle is PriceOracle {

/// @audit setPriceCap(), setFTokenToChainlinkOracle(), setMaxChainlinkOracleTimeDelay(), setFTokenToOracleType()
69:   interface IOndoPriceOracleV2 is IOndoPriceOracle {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracleV2.sol#L27

### [G&#x2011;11]  Use a more recent version of solidity
Use a solidity version of at least 0.8.0 to get overflow protection without `SafeMath`
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 2 instances of this issue:*

```solidity
File: contracts/lending/IOndoPriceOracle.sol

15:   pragma solidity 0.6.12;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/IOndoPriceOracle.sol#L15

```solidity
File: contracts/lending/OndoPriceOracle.sol

15:   pragma solidity 0.6.12;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L15

### [G&#x2011;12]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There is 1 instance of this issue:*

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

292       require(
293         (answeredInRound >= roundId) &&
294           (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
295         "Chainlink oracle price is stale"
296:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L292-L296

### [G&#x2011;13]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/CashManager.sol

93:     uint256 public immutable decimalsMultiplier;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L93

### [G&#x2011;14]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 16 instances of this issue:*

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

163       require(
164         msg.sender == guardian,
165         "CashKYCSenderFactory: You are not the Guardian"
166:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L163-L166

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

163       require(
164         msg.sender == guardian,
165         "CashKYCSenderReceiverFactory: You are not the Guardian"
166:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L163-L166

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

88        require(
89          !kycState[kycRequirementGroup][user],
90          "KYCRegistry: user already verified"
91:       );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L88-L91

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

63        require(
64          _getKYCStatus(_msgSender()),
65          "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
66:       );

70          require(
71            _getKYCStatus(from),
72            "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
73:         );

78          require(
79            _getKYCStatus(to),
80            "CashKYCSenderReceiver: `to` address must be KYC'd to receive tokens"
81:         );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L63-L66

```solidity
File: contracts/cash/token/CashKYCSender.sol

63        require(
64          _getKYCStatus(_msgSender()),
65          "CashKYCSender: must be KYC'd to initiate transfer"
66:       );

70          require(
71            _getKYCStatus(from),
72            "CashKYCSender: `from` address must be KYC'd to send tokens"
73:         );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L63-L66

```solidity
File: contracts/cash/token/Cash.sol

36        require(
37          hasRole(TRANSFER_ROLE, _msgSender()),
38          "Cash: must have TRANSFER_ROLE to transfer"
39:       );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L36-L39

```solidity
File: contracts/lending/OndoPriceOracle.sol

120       require(
121         CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
122         "cToken and fToken must have the same underlying asset if cToken nonzero"
123:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L120-L123

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

164       require(
165         fTokenToOracleType[fToken] == OracleType.MANUAL,
166         "OracleType must be Manual"
167:      );

211       require(
212         fTokenToOracleType[fToken] == OracleType.COMPOUND,
213         "OracleType must be Compound"
214:      );

215       require(
216         CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
217         "cToken and fToken must have the same underlying asset"
218:      );

255       require(
256         fTokenToOracleType[fToken] == OracleType.CHAINLINK,
257         "OracleType must be Chainlink"
258:      );

280       require(
281         fTokenToOracleType[fToken] == OracleType.CHAINLINK,
282         "fToken is not configured for Chainlink oracle"
283:      );

292       require(
293         (answeredInRound >= roundId) &&
294           (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
295         "Chainlink oracle price is stale"
296:      );

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L164-L167

### [G&#x2011;15]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 34 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

281     function setMintExchangeRate(
282       uint256 exchangeRate,
283       uint256 epochToSet
284:    ) external override updateEpoch onlyRole(SETTER_ADMIN) {

336     function setPendingMintBalance(
337       address user,
338       uint256 epoch,
339       uint256 oldBalance,
340       uint256 newBalance
341:    ) external updateEpoch onlyRole(MANAGER_ADMIN) {

366     function overrideExchangeRate(
367       uint256 correctExchangeRate,
368       uint256 epochToSet,
369       uint256 _lastSetMintExchangeRate
370:    ) external override updateEpoch onlyRole(MANAGER_ADMIN) {

392     function setMintExchangeRateDeltaLimit(
393       uint256 _exchangeRateDeltaLimit
394:    ) external override onlyRole(MANAGER_ADMIN) {

410     function setMintFee(
411       uint256 _mintFee
412:    ) external override onlyRole(MANAGER_ADMIN) {

433     function setMinimumDepositAmount(
434       uint256 _minimumDepositAmount
435:    ) external override onlyRole(MANAGER_ADMIN) {

452     function setFeeRecipient(
453       address _feeRecipient
454:    ) external override onlyRole(MANAGER_ADMIN) {

465     function setAssetRecipient(
466       address _assetRecipient
467:    ) external override onlyRole(MANAGER_ADMIN) {

546     function setEpochDuration(
547       uint256 _epochDuration
548:    ) external onlyRole(MANAGER_ADMIN) {

609     function setRedeemLimit(
610       uint256 _redeemLimit
611:    ) external onlyRole(MANAGER_ADMIN) {

707     function completeRedemptions(
708       address[] calldata redeemers,
709       address[] calldata refundees,
710       uint256 collateralAmountToDist,
711       uint256 epochToService,
712       uint256 fees
713:    ) external override updateEpoch onlyRole(MANAGER_ADMIN) {

803     function setAssetSender(
804       address newAssetSender
805:    ) external onlyRole(MANAGER_ADMIN) {

817     function setRedeemMinimum(
818       uint256 newRedeemMinimum
819:    ) external onlyRole(MANAGER_ADMIN) {

851     function setPendingRedemptionBalance(
852       address user,
853       uint256 epoch,
854       uint256 balance
855:    ) external updateEpoch onlyRole(MANAGER_ADMIN) {

896     function setKYCRequirementGroup(
897       uint256 _kycRequirementGroup
898:    ) external override onlyRole(MANAGER_ADMIN) {

907     function setKYCRegistry(
908       address _kycRegistry
909:    ) external override onlyRole(MANAGER_ADMIN) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L281-L284

```solidity
File: contracts/cash/factory/CashFactory.sol

75      function deployCash(
76        string calldata name,
77        string calldata ticker
78:     ) external onlyGuardian returns (address, address, address) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L75-L78

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

75      function deployCashKYCSender(
76        string calldata name,
77        string calldata ticker,
78        address registry,
79        uint256 kycRequirementGroup
80:     ) external onlyGuardian returns (address, address, address) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L75-L80

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

75      function deployCashKYCSenderReceiver(
76        string calldata name,
77        string calldata ticker,
78        address registry,
79        uint256 kycRequirementGroup
80:     ) external onlyGuardian returns (address, address, address) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L75-L80

```solidity
File: contracts/cash/kyc/KYCRegistryClientInitializable.sol

44      function __KYCRegistryClientInitializable_init(
45        address _kycRegistry,
46        uint256 _kycRequirementGroup
47:     ) internal onlyInitializing {

58      function __KYCRegistryClientInitializable_init_unchained(
59        address _kycRegistry,
60        uint256 _kycRequirementGroup
61:     ) internal onlyInitializing {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L44-L47

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

144     function assignRoletoKYCGroup(
145       uint256 kycRequirementGroup,
146       bytes32 role
147:    ) external onlyRole(REGISTRY_ADMIN) {

158     function addKYCAddresses(
159       uint256 kycRequirementGroup,
160       address[] calldata addresses
161:    ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {

175     function removeKYCAddresses(
176       uint256 kycRequirementGroup,
177       address[] calldata addresses
178:    ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L144-L147

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

34      function setKYCRequirementGroup(
35        uint256 group
36:     ) external override onlyRole(KYC_CONFIGURER_ROLE) {

40      function setKYCRegistry(
41        address registry
42:     ) external override onlyRole(KYC_CONFIGURER_ROLE) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L34-L36

```solidity
File: contracts/cash/token/CashKYCSender.sol

34      function setKYCRequirementGroup(
35        uint256 group
36:     ) external override onlyRole(KYC_CONFIGURER_ROLE) {

40      function setKYCRegistry(
41        address registry
42:     ) external override onlyRole(KYC_CONFIGURER_ROLE) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L34-L36

```solidity
File: contracts/lending/OndoPriceOracle.sol

92      function setFTokenToCToken(
93        address fToken,
94        address cToken
95:     ) external override onlyOwner {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L92-L95

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

130     function setPriceCap(
131       address fToken,
132       uint256 value
133:    ) external override onlyOwner {

145     function setFTokenToOracleType(
146       address fToken,
147       OracleType oracleType
148:    ) external override onlyOwner {

194     function setFTokenToCToken(
195       address fToken,
196       address cToken
197:    ) external override onlyOwner {

233     function setFTokenToChainlinkOracle(
234       address fToken,
235       address newChainlinkOracle
236:    ) external override onlyOwner {

309     function setMaxChainlinkOracleTimeDelay(
310       uint256 _maxChainlinkOracleTimeDelay
311:    ) external override onlyOwner {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L130-L133

### [G&#x2011;16]  Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*There are 3 instances of this issue:*

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

64:         _getKYCStatus(_msgSender()),

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L64

```solidity
File: contracts/cash/token/CashKYCSender.sol

64:         _getKYCStatus(_msgSender()),

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L64

```solidity
File: contracts/cash/token/Cash.sol

37:         hasRole(TRANSFER_ROLE, _msgSender()),

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L37


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 4 |  480 |
| [G&#x2011;02] | `<array>.length` should not be looked up in every loop of a `for`-loop | 4 |  12 |
| [G&#x2011;03] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 2 |  - |
| [G&#x2011;04] | Using `bool`s for storage incurs overhead | 1 |  17100 |
| [G&#x2011;05] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 2 |  10 |
| [G&#x2011;06] | Using `private` rather than `public` for constants, saves gas | 18 |  - |
| [G&#x2011;07] | Use custom errors rather than `revert()`/`require()` strings to save gas | 8 |  - |
| [G&#x2011;08] | Functions guaranteed to revert when called by normal users can be marked `payable` | 7 |  147 |

Total: 46 instances over 8 issues with **17749 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 4 instances of this issue:*

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

/// @audit name - (valid but excluded finding)
/// @audit symbol - (valid but excluded finding)
46      function initialize(
47        string memory name,
48        string memory symbol,
49        address kycRegistry,
50        uint256 kycRequirementGroup
51:     ) public initializer {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L46-L51

```solidity
File: contracts/cash/token/CashKYCSender.sol

/// @audit name - (valid but excluded finding)
/// @audit symbol - (valid but excluded finding)
46      function initialize(
47        string memory name,
48        string memory symbol,
49        address kycRegistry,
50        uint256 kycRequirementGroup
51:     ) public initializer {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L46-L51

### [G&#x2011;02]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 4 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit (valid but excluded finding)
961:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L961

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
127:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L127

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit (valid but excluded finding)
137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L137

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit (valid but excluded finding)
137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137

### [G&#x2011;03]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 2 instances of this issue:*

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
152:      require(msg.sender == guardian, "CashFactory: You are not the Guardian");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L152

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
87:       require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L87

### [G&#x2011;04]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
39:     mapping(uint256 => mapping(address => bool)) public kycState;

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L39

### [G&#x2011;05]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 2 instances of this issue:*

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
163:      for (uint256 i = 0; i < length; i++) {

/// @audit (valid but excluded finding)
180:      for (uint256 i = 0; i < length; i++) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L163

### [G&#x2011;06]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 18 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit (valid but excluded finding)
89:     uint256 public constant BPS_DENOMINATOR = 10_000;

/// @audit (valid but excluded finding)
122:    bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");

/// @audit (valid but excluded finding)
123:    bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");

/// @audit (valid but excluded finding)
124:    bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L89

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

/// @audit (valid but excluded finding)
45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/// @audit (valid but excluded finding)
46:     bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L44

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit (valid but excluded finding)
44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

/// @audit (valid but excluded finding)
45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/// @audit (valid but excluded finding)
46:     bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L44

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit (valid but excluded finding)
44:     bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

/// @audit (valid but excluded finding)
45:     bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/// @audit (valid but excluded finding)
46:     bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
31      bytes32 public constant _APPROVAL_TYPEHASH =
32        keccak256(
33          "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
34:       );

/// @audit (valid but excluded finding)
36:     bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L31-L34

```solidity
File: contracts/cash/token/CashKYCSenderReceiver.sol

/// @audit (valid but excluded finding)
26      bytes32 public constant KYC_CONFIGURER_ROLE =
27:       keccak256("KYC_CONFIGURER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L26-L27

```solidity
File: contracts/cash/token/CashKYCSender.sol

/// @audit (valid but excluded finding)
26      bytes32 public constant KYC_CONFIGURER_ROLE =
27:       keccak256("KYC_CONFIGURER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L26-L27

```solidity
File: contracts/cash/token/Cash.sol

/// @audit (valid but excluded finding)
22:     bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/Cash.sol#L22

### [G&#x2011;07]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 8 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit (valid but excluded finding)
965:        require(success, "Call Failed");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L965

```solidity
File: contracts/cash/factory/CashFactory.sol

/// @audit (valid but excluded finding)
131:        require(success, "Call Failed");

/// @audit (valid but excluded finding)
152:      require(msg.sender == guardian, "CashFactory: You are not the Guardian");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L131

```solidity
File: contracts/cash/factory/CashKYCSenderFactory.sol

/// @audit (valid but excluded finding)
141:        require(success, "Call Failed");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L141

```solidity
File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

/// @audit (valid but excluded finding)
141:        require(success, "Call Failed");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L141

```solidity
File: contracts/cash/kyc/KYCRegistry.sol

/// @audit (valid but excluded finding)
87:       require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");

/// @audit (valid but excluded finding)
92:       require(block.timestamp <= deadline, "KYCRegistry: signature expired");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L87

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit (valid but excluded finding)
297:      require(answer >= 0, "Price cannot be negative");

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L297

### [G&#x2011;08]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 7 instances of this issue:*

```solidity
File: contracts/cash/CashManager.sol

/// @audit (valid but excluded finding)
526:    function pause() external onlyRole(PAUSER_ADMIN) {

/// @audit (valid but excluded finding)
533:    function unpause() external onlyRole(MANAGER_ADMIN) {

/// @audit (valid but excluded finding)
596:    function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L526

```solidity
File: contracts/lending/OndoPriceOracle.sol

/// @audit (valid but excluded finding)
80:     function setPrice(address fToken, uint256 price) external override onlyOwner {

/// @audit (valid but excluded finding)
106:    function setOracle(address newOracle) external override onlyOwner {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L80

```solidity
File: contracts/lending/OndoPriceOracleV2.sol

/// @audit (valid but excluded finding)
163:    function setPrice(address fToken, uint256 price) external override onlyOwner {

/// @audit (valid but excluded finding)
182:    function setOracle(address newOracle) external override onlyOwner {

```
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L163
