## Ondo Finance Audit Report

## Gas Optimizations

### Increments should be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Also increments can be made pre-increment instead of post-increment, so `i++` would become `++i` where necessary.

https://github.com/ethereum/solidity/issues/10695

Instances include:

```solidity
CashManager._processRedemption(): for (uint256 i = 0; i < size; ++i);
CashManager._processRefund(): for (uint256 i = 0; i < size; ++i);
CashManager._checkAddressesKYC(): for (uint256 i = 0; i < size; ++i);

CashFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderReceiverFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

KYCRegistry.addKYCAddresses(): for (uint256 i = 0; i < length; i++);
KYCRegistry.removeKYCAddresses(): for (uint256 i = 0; i < length; i++);
```

The code would go from:

```solidity
for (uint256 i; i < numIterations; i++) {  
 // ...  
}  
```

To:

```solidity
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}  
```

### An array's length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Instances include:

```solidity
CashFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderReceiverFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);
```

The code would go from:

```solidity
for (uint256 i; i < arr.length; ++i) {  
 // ...  
}  
```

To:

```solidity
uint length = arr.length;
for (uint256 i; i < length; ++i) {  
 // ...  
}  
```

### Usage of double require instead of && operator should be considered

The usage of multiple require statements instead of one complex saves gas in some cases.

Instances include:

```solidity
CTokenCash.initialize(): require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
    
CTokenModified.initialize(): require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```

The code would go from:

```solidity
require(x == 0 && y == 0);
```

To:

```solidity
require(x == 0);
require(y == 0);
```

### Function ordering might be considered

There is additional value in renaming functions to be ordered based on the frequency you expect them to be used, saving average overall gas cost of interacting with your contract.

The function name might go from:

```solidity
setKYCRequirementGroup()
```

To:

```solidity
setKYCRequirementGroup_h3h() // 0x0000f83d
```

Use this tool if needed: https://emn178.github.io/solidity-optimize-name/

### No need to set default value to variables

If a variable is not set/initialized, the default value is assumed depending on data type, direct initialization of variables wastes gas.

Instances include:

```solidity
CashManager(): uint256 public mintFee = 0;
CashManager._processRedemption(): for (uint256 i = 0; i < size; ++i);
CashManager._processRefund(): for (uint256 i = 0; i < size; ++i);
CashManager._checkAddressesKYC(): for (uint256 i = 0; i < size; ++i);
CashManager.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

CashKYCSenderReceiverFactory.multiexcall(): for (uint256 i = 0; i < exCallData.length; ++i);

KYCRegistry.addKYCAddresses(): for (uint256 i = 0; i < length; i++);
KYCRegistry.removeKYCAddresses(): for (uint256 i = 0; i < length; i++);

CTokenCash.transferTokens(): uint startingAllowance = 0;

CTokenModified.transferTokens(): uint startingAllowance = 0;
```

The code would go from:

```solidity
uint a = 0;
```

To:

```solidity
uint a;
```

### Avoid compound assignment operator in state variables

Using compound assignment operators for state variables (like `state += x` or `state -= x`) is more expensive than using operator assignment (like `state = state + x` or `state = state - x`).

Instances include:

```solidity
CashManager._checkAndUpdateMintLimit(): mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
CashManager.transitionEpoch(): currentEpoch += epochDifference;
CashManager._checkAndUpdateMintLimit(): currentMintAmount += collateralAmountIn;
CashManager._checkAndUpdateRedeemLimit(): currentRedeemAmount += amount;
CashManager.requestRedemption(): redemptionInfoPerEpoch[currentEpoch].addressToBurnAmt[msg.sender] += amountCashToRedeem;
CashManager.requestRedemption(): redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;
CashManager._processRefund(): totalCashAmountRefunded += cashAmountBurned;
CashManager.setPendingRedemptionBalance(): redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
```

The code would go from:

```solidity
uint private _a;
function example() public {
    _a += 1;
}
```

To:

```solidity
uint private _a;
function example() public {
    _a = _a + 1;
}
```