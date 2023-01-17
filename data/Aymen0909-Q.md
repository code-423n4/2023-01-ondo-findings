# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1  | `getChainlinkOraclePrice` function can return null prices | Low | 1 |
| 2  | `maxChainlinkOracleTimeDelay` is too big for some tokens price feeds | Low | 1 |
| 3  | `owner` of `JumpRateModelV2` contract can not be changed | Low | 1 |
| 4  | Related data should be grouped in a `struct` | NC |  5 |
| 5  | Named return variables not used anywhere in the functions | NC | 1 |
| 6  | Use solidity time-based units | NC | 1 |

## Findings

### 1- `getChainlinkOraclePrice` function can return null prices :

The function `getChainlinkOraclePrice` is used in the `OndoPriceOracleV2` contract to get a token price using the Chainlink oracle, and the function will revert if the price returned by the oracle is not greater or equal than zero, but if the returned price is zero (which can happen if there is an isssue in the Chainlink oracle for some reason) the function will not revert and still return the null price this can have a negative impact on other functions (from other contracts) that uses this price in their internal logic.

### Risk : Low

### Proof of Concept

Issue occurs in the `getChainlinkOraclePrice` function below :

File: lending/OndoPriceOracleV2.sol [Line 277-301](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L277-L301)
```
function getChainlinkOraclePrice(
address fToken
) public view returns (uint256) {
    require(
        fTokenToOracleType[fToken] == OracleType.CHAINLINK,
        "fToken is not configured for Chainlink oracle"
    );
    ChainlinkOracleInfo memory chainlinkInfo = fTokenToChainlinkOracle[fToken];
    (
        uint80 roundId,
        int answer,
        ,
        uint updatedAt,
        uint80 answeredInRound
    ) = chainlinkInfo.oracle.latestRoundData();
    require(
        (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
        "Chainlink oracle price is stale"
    );
    /**
        @audit : it should be answer > 0 and not answer >= 0
        This to avoid the null price in case of chainlink oracle issue
    */
    require(answer >= 0, "Price cannot be negative");
    // Scale to decimals needed in Comptroller (18 decimal underlying -> 18 decimals; 6 decimal underlying -> 30 decimals)
    // Scales by same conversion factor as in Compound Oracle
    return uint256(answer) * chainlinkInfo.scaleFactor;
}
```

As you can see the `require()` check allow a null price and so the function call will succeed and will return zero.

### Mitigation

to avoid the issue of getting null prices the `getChainlinkOraclePrice` function shoud be modified to revert in that case, the follwing changes must be made :

```
function getChainlinkOraclePrice(
address fToken
) public view returns (uint256) {
    ...
    /**
        @audit : use answer > 0 
    */
    require(answer > 0, "Price cannot be negative");
    // Scale to decimals needed in Comptroller (18 decimal underlying -> 18 decimals; 6 decimal underlying -> 30 decimals)
    // Scales by same conversion factor as in Compound Oracle
    return uint256(answer) * chainlinkInfo.scaleFactor;
}
```

### 2- `maxChainlinkOracleTimeDelay` is too big for some tokens price feeds :

### Risk : Low

The value of `maxChainlinkOracleTimeDelay` is used in the `OndoPriceOracleV2` contract to check if the Chainlink oracle price is outdated or not and the current max delay value is set to `25h = 90000s` :

File: lending/OndoPriceOracleV2.sol [Line 77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)
```
uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
```

Even though this value may work for the majority of tokens certain tokens price feeds have a shorter heartbeat (heartbeat : the price feed update period) which may cause the Ondo oracle to give wrong (stale) prices for those tokens, for example `BTC/USD` or `COMP/USD` (there are others as well) have a heartbeat of `1h = 3600s` so if those tokens prices are not updated for 25h the oracle will be providing very old prices which could cause problems for the protocol or any other party using those prices. 

The different heartbeats of Chainlink oracle price feeds can be found [here](https://docs.chain.link/data-feeds/price-feeds/addresses/)

### Mitigation

To avoid this issue i would recommend to either set a lower value for `maxChainlinkOracleTimeDelay` variable (like 2h for example) or to use different delay values for some tokens (with lower heartbeats).

### 3- `owner` of `JumpRateModelV2` contract can not be changed :

The `JumpRateModelV2` contract does not use the OZ Ownable extension for handling ownership (compared to the OndoPriceOracle V1 & V2), instead the `owner` address state variable is declared and is set in the constructor and as the contract does not contain any functions to transfer ownership the owner can not be updated in the future which could cause problems.

### Risk : Low

### Proof of Concept

Issue occurs in the instance below :

File: lending/JumpRateModelV2.sol [Line 66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L66)
```
owner = owner_;
```

### Mitigation
Use the OZ Ownable extension for handling ownership of the `JumpRateModelV2` contract.

### 4- Related data should be grouped in a `struct` :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

### Risk : NON CRITICAL

### Proof of Concept

Instances include:

File: lending/OndoPriceOracleV2.sol [Line 55-73](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L55-L73)

```
55      mapping(address => OracleType) public fTokenToOracleType;
58      mapping(address => uint256) public fTokenToUnderlyingPrice;
62      mapping(address => address) public fTokenToCToken;
70      mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
73      mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```

### Mitigation

Group the related data in a struct and use one mapping :

```
struct fToken {
    OracleType fTokenToOracleType;
    uint256 fTokenToUnderlyingPrice;
    address fTokenToCToken;
    ChainlinkOracleInfo fTokenToChainlinkOracle;
    uint256 fTokenToUnderlyingPriceCap;
}
```

And it would be used as a state variable :

```
mapping(address => fToken) public _fTokens;
```

### 5- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

### Risk : NON CRITICAL

### Proof of Concept
Instances include:

File: cash/CashManager.sol [Line 784](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L784)
```
returns (uint256 totalCashAmountRefunded)
```

### Mitigation
Either use the named return variables inplace of the return statement or remove them.

### 6- Use solidity time-based units :

Solidity contains time-based units (seconds, minutes, hours, days and weeks) which should be used when declaring time based variables/constants instead of using literal numbers, this is done for better readability and to avoid mistakes.

### Risk : NON CRITICAL

### Proof of Concept
Instances include:

File: lending/OndoPriceOracleV2.sol [Line 77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)

```
uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
```

### Mitigation
Use time units when declaring time-based variables.
