## Gas Optimizations

| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW | 12 |
| [GAS-2](#GAS-2) | SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS | 3 |
| [GAS-3](#GAS-3) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 1 |

###  [GAS-1] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

*Instances (12)*:
```solidity
File: cash/CashManager.sol

750:      for (uint256 i = 0; i < size; ++i) {

786:      for (uint256 i = 0; i < size; ++i) {

933:      for (uint256 i = 0; i < size; ++i) {

961:      for (uint256 i = 0; i < exCallData.length; ++i) {

```

```solidity
File: cash/factory/CashFactory.sol

127:      for (uint256 i = 0; i < exCallData.length; ++i) {

```

```solidity
File: cash/factory/CashKYCSenderFactory.sol

137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```

```solidity
File: cash/factory/CashKYCSenderReceiverFactory.sol

137:      for (uint256 i = 0; i < exCallData.length; ++i) {

```

```solidity
File: cash/kyc/KYCRegistry.sol

163:      for (uint256 i = 0; i < length; i++) {

180:      for (uint256 i = 0; i < length; i++) {

```

```solidity
File: lending/CompoundLens.sol

85:      for (uint i = 0; i < cTokenCount; i++) {

135:     for (uint i = 0; i < cTokenCount; i++) {

167:     for (uint i = 0; i < cTokenCount; i++) {

```

###  [GAS-2] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Saves 3 gas per instance.

*Instances (3)*:
```solidity
File: lending/OndoPriceOracleV2.sol

293:         (answeredInRound >= roundId) &&

```

```solidity
File: lending/tokens/cCash/CTokenCash.sol

46:          accrualBlockNumber == 0 && borrowIndex == 0,

```

```solidity
File: lending/tokens/cCash/CTokenModified.sol

46:          accrualBlockNumber == 0 && borrowIndex == 0,

```

### [GAS-3] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

```solidity
File: cash/CashManager.sol

795:         return totalCashAmountRefunded;
```
[Link to Code](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L795)
---