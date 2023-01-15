1. COMPOUND ASSIGNMENT OPERATOR COSTS EXTRA GAS FOR STATE VARIABLES

<X += Y> uses more gas than <X = X + Y>
<X -= Y>  uses more gas than <X = X - Y>

Affected source code:
- [CashManager.sol#L221](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L221)
- [CashManager.sol#L582](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L582)
- [CashManager.sol#L630](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L630)
- [CashManager.sol#L649](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L649)
- [CashManager.sol#L792](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L792)

2. IN SOLIDITY 0.8+ <I++> AND <++I>  SHOULD BE UNCHECKED{I++} AND UNCHECKED{++I}

Because, there’s a default overflow check on unsigned integers. 
It’s possible to uncheck this in for-loops and save some gas at each iteration.

Affected source code:
- [CashManager.sol#L750](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L750)
- [CashManager.sol#L786](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L786)
- [CashManager.sol#L933](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L933)
- [CashManager.sol#L961](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L961)
- [CashFactory.sol#L127](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L127)
- [KYCRegistry.sol#L180](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L180)
- [KYCRegistry.sol#L163](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L163)

3. STRINGS LONGER THAN 32 BYTES IN REQUIRE() OR REVERT() COST EXTRA GAS

*“CashFactory: You are not the Guardian”* is 37 bytes.
```
    modifier onlyGuardian() {
        require(msg.sender == guardian, "CashFactory: You are not the Guardian");
        _;
      }
```
Affected source code:
- [CashFactory.sol#L152](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L152)
- [KYCRegistry.sol#L87](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L87)
- [KYCRegistry.sol#L89](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L89)