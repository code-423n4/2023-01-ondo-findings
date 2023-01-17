## Summary
### Low Risk
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| L-01 | CashManager.cash should not be immutable                                         |
| L-02 | Additional sanity checks                                                         |
| L-03 | Use `require` instead of `assert`                                                |
| L-04 | Avoid leftover ETH in `CashFactory`                                              |
| L-05 | Immutable addresses should have appropriate checks in constructor                |
| L-06 | Avoid static year variables                                                      |
| L-07 | static prices should not be used in oracles                                      |
| L-08 | `CTokenModified.repayBorrowFresh` can be DOSed                                   |

### Non-Critical
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| N-01 | Use the same Solidity version across the protocol                                |
| N-02 | Scientific notation can be used                                                  |
| N-03 | Redundant cast                                                                   |
| N-04 | Order of functions                                                               |
| N-05 | Comments consistency                                                             |
| N-06 | Native time should be used                                                       |
| N-07 | Avoid floating pragmas                                                           |


## Low
### [L‑01] CashManager.cash should not be immutable 

`Cash` is upgradeable. `CashManager` should not have `cash` has an immutable variable,
as it will not be able to work if `Cash` gets upgraded.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L43
```solidity
43: Cash public immutable cash
```

### [L‑02] Additional sanity checks


Add an additional sanity check: `correctExchangeRate != 0`, as a value of `0` would make `_getMintAmountForEpoch()` revert, leading to `claimMint()` not working.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L375
```solidity
375:   epochToExchangeRate[epochToSet] = correctExchangeRate;
```


### [L‑03] Use `require` instead of `assert`

As per Solidity's (documentation)[https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=assert#panic-via-assert-and-error-via-require]:

```
The assert function creates an error of type Panic(uint256). The same error is created by the compiler in certain situations as listed below.

Assert should only be used to test for internal errors, and to check invariants.
Properly functioning code should never create a Panic, not even on invalid external input.
If this happens, then there is a bug in your contract which you should fix
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97
```solidity
97:   assert(cashProxyAdmin.owner() == guardian);
```

### [L‑04] Avoid leftover ETH in `CashFactory`

Consider adding a check here after the loop that `msg.value = Sum(exCallDatavalues)`, to avoid having `ETH` leftover in CashFactory
The guardian could use that leftover ETH in future multiexcalls, but it is still better to avoid dust (or large amounts) remaining in `CashFactory`

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L123-L134
```solidity
123: function multiexcall(
124:     ExCallData[] calldata exCallData
125:   ) external payable override onlyGuardian returns (bytes[] memory results) {
126:     results = new bytes[](exCallData.length);
127:     for (uint256 i = 0; i < exCallData.length; ++i) {
128:       (bool success, bytes memory ret) = address(exCallData[i].target).call{
129:         value: exCallData[i].value
130:       }(exCallData[i].data);
131:       require(success, "Call Failed");
132:       results[i] = ret;
133:     }
134:   }
```

### [L‑05] Immutable addresses should have appropriate checks in constructor

Add a check to ensure `_sanctionsList != address(0)`.

Note: this variable was not mentioned in the C4udit output

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L57
```solidity
57:   sanctionsList = ISanctionsList(_sanctionsList);
```

### [L‑06] Avoid static year variables

static year variables will give an incorrect approximation on leap years, where `blocksPerYear` should be 2635200, leading to interest rates being higher than expected.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29
```solidity
29: uint public constant blocksPerYear = 2628000; 
```

### [L‑07] static prices should not be used in oracles  

It is dangerous for oracles to return a price set in storage.
Price volatility (or in the case of stablecoins, a depeg) can create arbitrage opportunities, given that this function is used to determine prices of assets in lending markets

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L61-L66
```solidity
61: function getUnderlyingPrice(
62:     address fToken
63:   ) external view override returns (uint256) {
64:     if (fTokenToUnderlyingPrice[fToken] != 0) {
65:       return fTokenToUnderlyingPrice[fToken];
66:     }
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L99-L102
```solidity
99: if (oracleType == OracleType.MANUAL) {
100:       // Get price stored in contract storage
101:       price = fTokenToUnderlyingPrice[fToken];
102:     }
```

### [L‑08] `CTokenModified.repayBorrowFresh` can be DOSed

If a user is trying to repay part of their loan, a malicious `payer` can front-run their call with an `amount` high enough so that when the borrower call is processed, the amount they are trying to repay is lower than the account's borrowing balance, causing a revert [here](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L818).
Note that this only arises if the user is trying to repay part of their loan - ie not calling `repayAmount == type(uint).max`.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L818
```solidity
818: uint accountBorrowsNew = accountBorrowsPrev - actualRepayAmount;
```

A solution would be to compare `repayAmount` with `accountBorrowsPrev` to prevent underflow from happening.

```diff
-796: uint repayAmountFinal = repayAmount == type(uint).max
+796: uint repayAmountFinal = repayAmount >= accountBorrowsPrev
797:       ? accountBorrowsPrev
798:       : repayAmount;
```



## Non-critical
### [N-01] Use the same Solidity version across the protocol  


https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L15
```solidity
15: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2
```solidity
2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L15
```solidity
15: pragma solidity 0.6.12;
```

### [N-02] Scientific notation can be used 


https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L58
```solidity
58: uint256 public minimumDepositAmount = 10_000 //@audit - change to 1e4
89: uint256 public constant BPS_DENOMINATOR = 10_000; //@audit - change to 1e4
```

### [N-03] Redundant cast


https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L962
```solidity
962: (bool success, bytes memory ret) = address(exCallData[i].target).call
//@audit = exCallData[i].target is already an address
```

### [N-04] Order of functions

modifier should be placed before functions, as per the Solidity documentation [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions)
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L152-L155
```solidity
152:   modifier onlyGuardian() {
153:     require(msg.sender == guardian, "CashFactory: You are not the Guardian");
154:     _;
155:   }
```

### [N-05] Comments consistency

For consistency, comments should specify `jumpMultiplierPerYear` and `kink_` are scaled by 1e18 - as it is stated for `baseRatePerYear` and `multiplierPerYear`
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L53-L56
```solidity
53: * @param baseRatePerYear The approximate target base APR, as a mantissa (scaled by 1e18)
54:    * @param multiplierPerYear The rate of increase in interest rate wrt utilization (scaled by 1e18)
55:    * @param jumpMultiplierPerYear The multiplierPerBlock after hitting a specified utilization point
56:    * @param kink_ The utilization point at which the jump multiplier is applied
```

### [N-06] Native time should be used

use Native time units instead of a number of seconds
https://github.com/code-423n4/2023-01-ondo/blob/main/lending/OndoPriceOracleV2.sol#L77
```solidity
77: uint256 public maxChainlinkOracleTimeDelay = 90000; //@audit 25 hours;
```

### [N-07] Avoid floating pragmas

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2
```solidity
2: pragma solidity ^0.8.10;
```
