# Gas-Report

|                                                                                | Issue                                                           | Instances |
| ------------------------------------------------------------------------------ | :-------------------------------------------------------------- | :-------: |
| [GAS-1](#gas-1-consider-using-ir-cogen-compiler-pipeline-for-gas-optimization) | Consider using IR Codgen compiler pipeline for gas optimization |    N/A    |
| [GAS-2](#gas-2-declare-constructor-as-payable)                                 | Declare Constructor as payable.                                 |    18     |
| [GAS-3](#-gas-3-function-modifiers-can-be-inefficient)                         | Function modifiers can be inefficient                           |     1     |
| [GAS-4](#gas-4-the-increment-in-for-loop-post-condition-can-be-made-unchecked) | The increment in for loop post condition can be made unchecked  |     9     |

---
## GAS-1: Consider using IR Cogen compiler pipeline for gas optimization
- Description: The IR-based code generator was introduced with an aim to not only allow code generation to be more transparent and auditable but also to enable more powerful optimization passes that span across functions. Enabling {viaIR: true} in the compiler settings will enable the IR-based code generator. This will allow the compiler to use more powerful optimization passes that span across functions. The gas savings will be in the range of 5-10%.
- Caution: If you use via-ir use the latest compiler version 0.8.17: (The compiler version 0.8.17 has a fix for a bug in the code generator that could lead to the code generator producing invalid code for certain contracts. This bug was introduced in version 0.8.16 and fixed in version 0.8.17.)
- Location: Project Wide

---
## GAS-2: Declare Constructor as payable.
- Description: You eliminate the payable check in the init code fragment Thereby reducing deployment byte code thus gas during deployment.
- Location: Project Wide (there are 18 constructors without the payable keyword: excluding compound contracts)
- count: 18 in 18 files

---
## GAS-3 Function modifiers can be inefficient
- Description: The code of modifiers is inlined inside the modified function, thus adding up size and costing gas. Limit the modifiers. Internal functions are not inlined, but called as separate functions. They are slightly more expensive at run time, but save a lot of redundant byte code in deployment, if used more than once
- Location: [CashFactory.sol#L152](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L152)
- count: 1
- Recommendation: Use internal functions instead of modifiers
```solidity
// Before

  modifier onlyGuardian() {
      require(msg.sender == guardian, "CashFactory: You are not the Guardian");
      _;
  }
```
```solidity
// after
    modifier onlyGuardian() {
        onlyGuardian();
        _;
    }

    function OnlyGuardian() internal view {
        require(msg.sender == guardian, "CashFactory: You are not the Guardian");
    }
```
---
## GAS-4: The increment in for loop post condition can be made unchecked.
- Description: The for loop post condition, i.e., i++ involves checked arithmetic, which is not required. This is because the value of i is always strictly less than length <= 2\**256 - 1. Therefore, the theoretical maximum value of i to enter the for-loop body is 2**256 - 2. This means that the i++ in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler. Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks.
- Location: [KYCRegistry.sol#L163 L181](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163)[CashManger.sol#L933, L961, L786, L750](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933) [CashFactory.sol#L127](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127) [CashKYCSenderReceiverFactory.sol#L137]()
- count: 9
