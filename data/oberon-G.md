### REFACTOR SIMILAR REQUIRE()/REVERT()
---
```javascript
function oracleType() private view {
    require(
      fTokenToOracleType[fToken] == OracleType.CHAINLINK,
      "OracleType must be Chainlink"
    );
}
255: oracleType();
280: oracleType();
```

[OndoPriceOracleV2:255_280](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L255)


### USE MULTIPLE REQUIRE() FOR &&
---
```javascript
    require(answeredInRound >= roundId);
    require((updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );
```

[OndoPriceOracleV2:293](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L293)
[CTokenCash:46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L46)
[CTokenModified:46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L46)


### USE FUNCTIONS INSTEAD OF MODIFIERS
---
```javascript
function updateEpoch() private view {
    transitionEpoch();
}
```

[CashManager:557](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L557)
[CashManager:883](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L883)
[CashFactory:151](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L151)
[CashKYCSenderFactory:162](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L162)
[CashKYCSenderReceiverFactory:162](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L162)


### X += Y COSTS MORE THAN X = X + Y FOR STATE VARIABLES
---
```javascript
    currentEpoch = currentEpoch + epochDifference;
```

[CashManager.sol:582_630_649_681](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L582)
