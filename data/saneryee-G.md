# Gas Optimizations Report

|         | Issue                                                                                                              | Instances |
| ------- | :----------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables                                    |     4     |
| [G-002] | Splitting require() statements that use `&&` saves gas                                                             |     3     |
| [G-003] | Don't use `_msgSender()` if not supporting EIP-2771                                                                |     2     |
| [G-004] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate |     5     |
| [G-005] | Multiple accesses of a `mapping/array` should use a local variable cache                                           |    26     |
| [G-006] | internal functions only called once can be inlined to save gas                                                     |     7     |
| [G-007] | Use a more recent version of solidity                                                                              |    10     |
| [G-008] | Replace modifier with function                                                                                     |     6     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:4

[contracts/cash/CashManager.sol#L792](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L792)

```solidity
792:    totalCashAmountRefunded += cashAmountBurned;
```

[contracts/cash/CashManager.sol#L649](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L649)

```solidity
649:    currentRedeemAmount += amount;
```

[contracts/cash/CashManager.sol#L582](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L582)

```solidity
582:    currentEpoch += epochDifference;
```

[contracts/cash/CashManager.sol#L630](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L630)

```solidity
630:    currentMintAmount += collateralAmountIn;
```

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:3

[contracts/lending/OndoPriceOracleV2.sol#L292-L296](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L292-L296)

```solidity
292:    require(
293:          (answeredInRound >= roundId) &&
294:            (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
295:          "Chainlink oracle price is stale"
296:        );
```

[contracts/lending/tokens/cCash/CTokenCash.sol#L45-L48](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cCash/CTokenCash.sol#L45-L48)

```solidity
45:    require(
46:          accrualBlockNumber == 0 && borrowIndex == 0,
47:          "market may only be initialized once"
48:        );
```

[contracts/lending/tokens/cToken/CTokenModified.sol#L45-L48](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cToken/CTokenModified.sol#L45-L48)

```solidity
45:    require(
46:          accrualBlockNumber == 0 && borrowIndex == 0,
47:          "market may only be initialized once"
48:        );
```

## [G-003] Don't use `_msgSender()` if not supporting EIP-2771

### Impact

The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption.

### Findings

Total:2

[contracts/cash/token/CashKYCSender.sol#L64](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/token/CashKYCSender.sol#L64)

```solidity
64:    _getKYCStatus(_msgSender()),
```

[contracts/cash/token/CashKYCSenderReceiver.sol#L64](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/token/CashKYCSenderReceiver.sol#L64)

```solidity
64:    _getKYCStatus(_msgSender()),
```

### Recommendation

Replace `_msgSender()` with `msg.sender` if there is no mechanism to support meta-transactions like EIP-2771 implemented.

## [G-004] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:5

[contracts/lending/OndoPriceOracleV2.sol#L55-L58](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L55-L58)

```solidity
55:    mapping(address => OracleType) public fTokenToOracleType;
56:
57:      /// @notice Contract storage for fToken's underlying asset prices
58:      mapping(address => uint256) public fTokenToUnderlyingPrice;
```

[contracts/lending/OndoPriceOracleV2.sol#L70-L73](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L70-L73)

```solidity
70:    mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
71:
72:      /// @notice Price cap for the underlying asset of an fToken. Optional.
73:      mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```

[contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L94-L97](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L94-L97)

```solidity
94:    mapping(address => uint) internal accountTokens;
95:
96:      // Approved token transfer amounts on behalf of others
97:      mapping(address => mapping(address => uint)) internal transferAllowances;
```

[contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L92-L95](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L92-L95)

```solidity
92:    mapping(address => uint) internal accountTokens;
93:
94:      // Approved token transfer amounts on behalf of others
95:      mapping(address => mapping(address => uint)) internal transferAllowances;
```

[contracts/cash/CashManager.sol#L79-L82](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L79-L82)

```solidity
79:    mapping(uint256 => RedemptionRequests) public redemptionInfoPerEpoch;
80:
81:      // Mapping used for getting the exchange rate during a given epoch
82:      mapping(uint256 => uint256) public epochToExchangeRate;
```

## [G-005] Multiple accesses of a `mapping/array` should use a local variable cache

### Impact

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` (**与[G-001]类似**)

### Findings

Total:26

[contracts/lending/OndoPriceOracle.sol#L82](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracle.sol#L82)

```solidity
82:    fTokenToUnderlyingPrice[fToken] = price;
```

[contracts/lending/OndoPriceOracle.sol#L124](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracle.sol#L124)

```solidity
124:    fTokenToCToken[fToken] = cToken;
```

[contracts/cash/kyc/KYCRegistry.sol#L103](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/kyc/KYCRegistry.sol#L103)

```solidity
103:    kycState[kycRequirementGroup][user] = true;
```

[contracts/cash/kyc/KYCRegistry.sol#L164](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/kyc/KYCRegistry.sol#L164)

```solidity
164:    kycState[kycRequirementGroup][addresses[i]] = true;
```

[contracts/cash/kyc/KYCRegistry.sol#L181](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/kyc/KYCRegistry.sol#L181)

```solidity
181:    kycState[kycRequirementGroup][addresses[i]] = false;
```

[contracts/cash/kyc/KYCRegistry.sol#L148](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/kyc/KYCRegistry.sol#L148)

```solidity
148:    kycGroupRoles[kycRequirementGroup] = role;
```

[contracts/lending/OndoPriceOracleV2.sol#L169](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L169)

```solidity
169:    fTokenToUnderlyingPrice[fToken] = price;
```

[contracts/lending/OndoPriceOracleV2.sol#L219](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L219)

```solidity
219:    fTokenToCToken[fToken] = cToken;
```

[contracts/lending/OndoPriceOracleV2.sol#L149](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L149)

```solidity
149:    fTokenToOracleType[fToken] = oracleType;
```

[contracts/lending/OndoPriceOracleV2.sol#L165](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L165)

```solidity
165:    fTokenToOracleType[fToken] == OracleType.MANUAL,
```

[contracts/lending/OndoPriceOracleV2.sol#L212](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L212)

```solidity
212:    fTokenToOracleType[fToken] == OracleType.COMPOUND,
```

[contracts/lending/OndoPriceOracleV2.sol#L256](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L256)

```solidity
256:    fTokenToOracleType[fToken] == OracleType.CHAINLINK,
```

[contracts/lending/OndoPriceOracleV2.sol#L281](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L281)

```solidity
281:    fTokenToOracleType[fToken] == OracleType.CHAINLINK,
```

[contracts/lending/OndoPriceOracleV2.sol#L256](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L256)

```solidity
256:    fTokenToOracleType[fToken] == OracleType.CHAINLINK,
```

[contracts/lending/OndoPriceOracleV2.sol#L281](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L281)

```solidity
281:    fTokenToOracleType[fToken] == OracleType.CHAINLINK,
```

[contracts/lending/OndoPriceOracleV2.sol#L135](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L135)

```solidity
135:    fTokenToUnderlyingPriceCap[fToken] = value;
```

[contracts/cash/CashManager.sol#L754](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L754)

```solidity
754:    redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;
```

[contracts/cash/CashManager.sol#L790](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L790)

```solidity
790:    redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;
```

[contracts/cash/CashManager.sol#L869](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L869)

```solidity
869:    redemptionInfoPerEpoch[epoch].addressToBurnAmt[user] = balance;
```

[contracts/cash/CashManager.sol#L306](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L306)

```solidity
306:    epochToExchangeRate[epochToSet] = exchangeRate;
```

[contracts/cash/CashManager.sol#L315](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L315)

```solidity
315:    epochToExchangeRate[epochToSet] = exchangeRate;
```

[contracts/cash/CashManager.sol#L306](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L306)

```solidity
306:    epochToExchangeRate[epochToSet] = exchangeRate;
```

[contracts/cash/CashManager.sol#L315](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L315)

```solidity
315:    epochToExchangeRate[epochToSet] = exchangeRate;
```

[contracts/cash/CashManager.sol#L375](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L375)

```solidity
375:    epochToExchangeRate[epochToSet] = correctExchangeRate;
```

[contracts/cash/CashManager.sol#L259](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L259)

```solidity
259:    mintRequestsPerEpoch[epochToClaim][user] = 0;
```

[contracts/cash/CashManager.sol#L348](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L348)

```solidity
348:    mintRequestsPerEpoch[epoch][user] = newBalance;
```

## [G-006] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:7

[contracts/lending/OndoPriceOracle.sol#L119](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracle.sol#L119)

```solidity
119:    function _setFTokenToCToken(address fToken, address cToken) internal {
```

[contracts/lending/OndoPriceOracleV2.sol#L210](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L210)

```solidity
210:    function _setFTokenToCToken(address fToken, address cToken) internal {
```

[contracts/lending/OndoPriceOracleV2.sol#L324](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracleV2.sol#L324)

```solidity
324:    function _min(uint256 a, uint256 b) internal pure returns (uint256) {
```

[contracts/lending/tokens/cCash/CTokenCash.sol#L1198](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cCash/CTokenCash.sol#L1198)

```solidity
1198:    function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
```

[contracts/lending/tokens/cCash/CTokenCash.sol#L1253](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cCash/CTokenCash.sol#L1253)

```solidity
1253:    function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
```

[contracts/lending/tokens/cToken/CTokenModified.sol#L1201](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cToken/CTokenModified.sol#L1201)

```solidity
1201:    function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
```

[contracts/lending/tokens/cToken/CTokenModified.sol#L1256](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cToken/CTokenModified.sol#L1256)

```solidity
1256:    function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
```

## [G-007] Use a more recent version of solidity

### Impact

- Use a solidity version of at least `0.8.2` to get simple compiler automatic inlining
- Use a solidity version of at least `0.8.3` to get better struct packing and cheaper multiple storage reads
- Use a solidity version of at least `0.8.4` to get custom errors, which are cheaper at deployment than `revert()/require()` strings
- Use a solidity version of at least `0.8.10` to have external calls skip contract existence checks if the external call has a return value

### Findings

Total:10

[contracts/lending/OndoPriceOracle.sol#L15](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/OndoPriceOracle.sol#L15)

```solidity
15:    pragma solidity 0.6.12;
```

[contracts/lending/JumpRateModelV2.sol#L1](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/JumpRateModelV2.sol#L1)

```solidity
1:    pragma solidity ^0.5.16;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7)

```solidity
7:    pragma solidity ^0.5.12;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L138](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L138)

```solidity
138:    pragma solidity ^0.5.12;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L181](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L181)

```solidity
181:    pragma solidity ^0.5.12;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L647](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L647)

```solidity
647:    pragma solidity ^0.5.12;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L296](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L296)

```solidity
296:    pragma solidity ^0.5.16;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L302](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L302)

```solidity
302:    pragma solidity ^0.5.16;
```

[contracts/lending/tokens/cErc20ModifiedDelegator.sol#L324](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cErc20ModifiedDelegator.sol#L324)

```solidity
324:    pragma solidity ^0.5.16;
```

[contracts/lending/IOndoPriceOracle.sol#L15](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/IOndoPriceOracle.sol#L15)

```solidity
15:    pragma solidity 0.6.12;
```

### Recommendation

Current version: 0.8.17 (2022-11)

## [G-008] Replace modifier with function

### Impact

Modifiers make code more elegant, but cost more than normal functions.

### Findings

Total:6

[contracts/cash/factory/CashFactory.sol#L151](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/factory/CashFactory.sol#L151)

```solidity
151:    modifier onlyGuardian() {
```

[contracts/cash/factory/CashKYCSenderFactory.sol#L162](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/factory/CashKYCSenderFactory.sol#L162)

```solidity
162:    modifier onlyGuardian() {
```

[contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L162](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L162)

```solidity
162:    modifier onlyGuardian() {
```

[contracts/cash/CashManager.sol#L557](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/cash/CashManager.sol#L557)

```solidity
557:    modifier updateEpoch() {
```

[contracts/lending/tokens/cCash/CTokenCash.sol#L1434](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cCash/CTokenCash.sol#L1434)

```solidity
1434:    modifier nonReentrant() {
```

[contracts/lending/tokens/cToken/CTokenModified.sol#L1437](https://github.com/code-423n4/2023-01-ondo/tree/main//contracts/lending/tokens/cToken/CTokenModified.sol#L1437)

```solidity
1437:    modifier nonReentrant() {
```
