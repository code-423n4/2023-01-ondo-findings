# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1 | Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct | 5 |
| 2 | State variables only set in the constructor should be declared `immutable`  | 1 |
| 3 | `storage` variable should be cached into `memory` variables instead of re-reading them  |  4 |
| 4 | Use `unchecked` blocks to save gas  |  4 |
| 5 | Unecessary initialization of `currentEpoch` in the constructor | 1 |
| 6 | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  | 8 |
| 7 | `require()` strings longer than 32 bytes cost extra gas | 9 |
| 8 | Splitting `require()` statements that uses && saves gas | 1 |
| 9 | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow (for & while loops) |  9  |


## Findings

### 1- Multiple address/IDs mappings can be combined into a single mapping of an address/id to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 5 instances of this issue :

File: lending/OndoPriceOracleV2.sol [Line 55-73](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L55-L73)

```
55      mapping(address => OracleType) public fTokenToOracleType;
58      mapping(address => uint256) public fTokenToUnderlyingPrice;
62      mapping(address => address) public fTokenToCToken;
70      mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
73      mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct fToken {
    OracleType fTokenToOracleType;
    uint256 fTokenToUnderlyingPrice;
    address fTokenToCToken;
    ChainlinkOracleInfo fTokenToChainlinkOracle;
    uint256 fTokenToUnderlyingPriceCap;
}
    
mapping(address => fToken) public _fTokens;
```

### 2- State variables only set in the constructor should be declared `immutable` :

The state variables only set in the constructor should be declared `immutable`, this avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

There is 1 instance of this issue:

File: lending/JumpRateModelV2.sol [Line 24](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L24)
```
address public owner;
```

### 3- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 4 instances of this issue :

File: contracts/cash/CashManager.sol [Line 221-229](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L221-L229)

```
mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;

emit MintRequested(
    msg.sender,
    currentEpoch,
    collateralAmountIn,
    depositValueAfterFees,
    feesInCollateral
);  
```

The value of `currentEpoch` is read twice from storage and its value doesn't change so in should be cached into memory, the code should be modified as follow :
```
uint256 _epoch = currentEpoch;
mintRequestsPerEpoch[_epoch][msg.sender] += depositValueAfterFees;

emit MintRequested(
    msg.sender,
    _epoch,
    collateralAmountIn,
    depositValueAfterFees,
    feesInCollateral
);    
```

File: contracts/cash/CashManager.sol [Line 249-268](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L249-L268)

```
if (epochToExchangeRate[epochToClaim] == 0) {
    revert ExchangeRateNotSet();
}

...

emit MintCompleted(
    user,
    cashOwed,
    collateralDeposited,
    epochToExchangeRate[epochToClaim],
    epochToClaim
);   
```

The value of `epochToExchangeRate[epochToClaim]` is read twice from storage and its value doesn't change so in should be cached into memory, the code should be modified as follow :
```
uint256 _epochToExchangeRate = epochToExchangeRate[epochToClaim];
if (_epochToExchangeRate == 0) {
    revert ExchangeRateNotSet();
}

...

emit MintCompleted(
    user,
    cashOwed,
    collateralDeposited,
    _epochToExchangeRate,
    epochToClaim
);    
```

File: contracts/cash/CashManager.sol [Line 296-318](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L296-L318)

```
if (exchangeRate > lastSetMintExchangeRate) {
    rateDifference = exchangeRate - lastSetMintExchangeRate;
} else if (exchangeRate < lastSetMintExchangeRate) {
    rateDifference = lastSetMintExchangeRate - exchangeRate;
}

uint256 maxDifferenceThisEpoch = (lastSetMintExchangeRate *
    exchangeRateDeltaLimit) / BPS_DENOMINATOR;

if (rateDifference > maxDifferenceThisEpoch) {
    epochToExchangeRate[epochToSet] = exchangeRate;
    _pause();
    emit MintExchangeRateCheckFailed(
    epochToSet,
    lastSetMintExchangeRate,
    exchangeRate
    );
} else {
    uint256 oldExchangeRate = lastSetMintExchangeRate;
    epochToExchangeRate[epochToSet] = exchangeRate;
    lastSetMintExchangeRate = exchangeRate;
    emit MintExchangeRateSet(epochToSet, oldExchangeRate, exchangeRate);
}    
```

In the code above the value of `lastSetMintExchangeRate` is read multiple times from storage and its value doesn't change till the last line so it should be cached into memory, the code should be modified as follow :

```
uint256 oldExchangeRate = lastSetMintExchangeRate;
if (exchangeRate > oldExchangeRate) {
    rateDifference = exchangeRate - oldExchangeRate;
} else if (exchangeRate < oldExchangeRate) {
    rateDifference = oldExchangeRate - exchangeRate;
}

uint256 maxDifferenceThisEpoch = (oldExchangeRate *
    exchangeRateDeltaLimit) / BPS_DENOMINATOR;

if (rateDifference > maxDifferenceThisEpoch) {
    epochToExchangeRate[epochToSet] = exchangeRate;
    _pause();
    emit MintExchangeRateCheckFailed(
    epochToSet,
    oldExchangeRate,
    exchangeRate
    );
} else {
    epochToExchangeRate[epochToSet] = exchangeRate;
    lastSetMintExchangeRate = exchangeRate;
    emit MintExchangeRateSet(epochToSet, oldExchangeRate, exchangeRate);
}      
```

File: contracts/cash/CashManager.sol [Line 577-586](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L577-L586)

```
uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
    epochDuration;
if (epochDifference > 0) {
    currentRedeemAmount = 0;
    currentMintAmount = 0;
    currentEpoch += epochDifference;
    currentEpochStartTimestamp =
    block.timestamp -
    (block.timestamp % epochDuration);
}
```

The value of `epochDuration` is read twice from storage and its value doesn't change so in should be cached into memory, the code should be modified as follow :
```
uint256 duration = epochDuration;
uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
    duration;
if (epochDifference > 0) {
    currentRedeemAmount = 0;
    currentMintAmount = 0;
    currentEpoch += epochDifference;
    currentEpochStartTimestamp =
    block.timestamp -
    (block.timestamp % duration);
}  
```

### 4- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 4 instances of this issue:

File: contracts/cash/CashManager.sol [Line 210](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L210)
```
uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral; 
```

The above operation cannot underflow due to the check because the `feesInCollateral` is always less or equal to `collateralAmountIn` so the operation should be marked as `unchecked`. 

File: contracts/cash/CashManager.sol [Line 296-300](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L296-L300)

```
if (exchangeRate > lastSetMintExchangeRate) {
    rateDifference = exchangeRate - lastSetMintExchangeRate;
} else if (exchangeRate < lastSetMintExchangeRate) {
    rateDifference = lastSetMintExchangeRate - exchangeRate;
}
```

The two substractions in the code above cannot underflow because of the if check that precede them so they both should be marked as `unchecked`. 

File: contracts/cash/CashManager.sol [Line 626-630](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L626-L630)

```
if (collateralAmountIn > mintLimit - currentMintAmount) {
    revert MintExceedsRateLimit();
}

currentMintAmount += collateralAmountIn;
```

In the code above the value of `currentMintAmount` cannot overflow because the check before it ensures that we always have `currentMintAmount` less or equal to `mintLimit` even after the addition of `collateralAmountIn`, so the operation should be marked as `unchecked`. 

File: contracts/cash/CashManager.sol [Line 645-649](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L645-L649)

```
if (amount > redeemLimit - currentRedeemAmount) {
    revert RedeemExceedsRateLimit();
}

currentRedeemAmount += amount;
```

In the code above the value of `currentRedeemAmount` cannot overflow because the check before it ensures that we always have `currentRedeemAmount` less or equal to `redeemLimit` even after the addition of `amount`, so the operation should be marked as `unchecked`. 

### 5- Unecessary initialization of `currentEpoch` in the constructor :

In the constructor of `CashManager` contract the value of `currentEpoch` is set to be equal to its current value which is just redundant and cost more gas, if this is an error then the correct value should be added otherwise this line of code should be removed to save gas at the deployment.

File: cash/CashManager.sol [Line 168](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L168)
```
currentEpoch = currentEpoch;
```

### 6- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 8 instances of this issue:

```
File: contracts/cash/CashManager.sol

221     mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
582     currentEpoch += epochDifference;
630     currentMintAmount += collateralAmountIn;
649     currentRedeemAmount += amount;
678     redemptionInfoPerEpoch[currentEpoch].addressToBurnAmt[msg.sender] += amountCashToRedeem;
681     redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
864     redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
867     redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```

### 7- `require()` strings longer than 32 bytes cost extra gas :

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

There are 9 instances of this issue :

```
File: cash/factory/CashKYCSenderReceiverFactory.sol

163     require(
            msg.sender == guardian,
            "CashKYCSenderReceiverFactory: You are not the Guardian"
        );

File: cash/token/CashKYCSender.sol

63      require(
            _getKYCStatus(_msgSender()),
            "CashKYCSender: must be KYC'd to initiate transfer"
        );
70      require(
            _getKYCStatus(from),
            "CashKYCSender: `from` address must be KYC'd to send tokens"
        );

File: cash/token/CashKYCSenderReceiver.sol

63      require(
            _getKYCStatus(_msgSender()),
            "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
        );
70      require(
            _getKYCStatus(from),
            "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
        );
78      require(
            _getKYCStatus(from),
            "CashKYCSenderReceiver: `from` address must be KYC'd to send tokens"
        );

File: lending/OndoPriceOracle.sol

120     require(
            CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
            "cToken and fToken must have the same underlying asset if cToken nonzero"
        );

File: lending/OndoPriceOracleV2.sol

215     require(
            CTokenLike(fToken).underlying() == CTokenLike(cToken).underlying(),
            "cToken and fToken must have the same underlying asset"
        );
280     require(
            fTokenToOracleType[fToken] == OracleType.CHAINLINK,
            "fToken is not configured for Chainlink oracle"
        );
```

### 8- Splitting `require()` statements that uses && saves gas :

There is 1 instance of this issue :

```
File: lending/OndoPriceOracleV2.sol

192     require(
            (answeredInRound >= roundId) &&
                (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
            "Chainlink oracle price is stale"
        );
```

### 9- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 9 instances of this issue:
 
```
File: contracts/cash/CashManager.sol

750     for (uint256 i = 0; i < size; ++i)
786     for (uint256 i = 0; i < size; ++i)
933     for (uint256 i = 0; i < size; ++i)
961     for (uint256 i = 0; i < exCallData.length; ++i)

File: contracts/cash/factory/CashFactory.sol

127     for (uint256 i = 0; i < exCallData.length; ++i) 

File: contracts/cash/factory/CashKYCSenderFactory.sol

137     for (uint256 i = 0; i < exCallData.length; ++i) 

File: contracts/cash/factory/CashKYCSenderReceiverFactory.sol

137     for (uint256 i = 0; i < exCallData.length; ++i) 

File: contracts/cash/kyc/KYCRegistry.sol

163     for (uint256 i = 0; i < length; i++) 
180     for (uint256 i = 0; i < length; i++) 
```