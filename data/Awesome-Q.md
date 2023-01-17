# 1. Improve the Readability by following modularity principles

To make your Solidity code more modern and readable, it is recommended to update your import statements to only include the specific contracts or objects that you need.

This practice, known as modular programming, helps to keep the code cleaner and more organized.

To avoid unnecessarily polluting the source code with unused contracts or objects, it is recommended to use specific imports with curly braces. An example of this syntax is shown below:

```solidity
import {contract1, contract2} from "filename.sol";
```

By using specific imports in this way, you can ensure that your code follows the principles of modularity and is as clear and maintainable as possible.

Affected line of code:

- [CashKYCSender.sol#L18-L19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L18-L19)


# 2. inadequate NatSpec

Contracts can use the Ethereum Natural Language Specification Format (NatSpec) to provide detailed documentation for functions, return variables, and other elements of the contract. This is done using a special type of comment within the contract code.

You can find more information about NatSpec and its usage in Solidity contracts at the following link:
- [NatSpec Format](https://docs.soliditylang.org/en/v0.8.16/natspec-format.html)

This is present in 4 file:
- [Proxy.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol)
- [Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)
- [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
- [CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)




# 3. Use the `delete` operator to clear variables, rather than assigning a value of `false`.

To clear variables, consider using the `delete` operator rather than assigning to `false`, because this conveys the intention more clearly and is more idiomatic.

As an example on [line 181](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L181) you can refactor the code like so:

```solidity
Line 181:    delete kycState[kycRequirementGroup][addresses[i]];
```

Affected line of code:

- [KYCRegistry.sol#L181](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L181)
