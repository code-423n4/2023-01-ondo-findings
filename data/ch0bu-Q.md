## 1. Use a more recent version of Solidity, and avoid floating pragma statements

- Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)` 
- Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`
- Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

Most contracts in scope use up to date version of 0.8.16, but there are some that use ^0.8.10, 0.6.12, ^0.5.16. These need to be updated, especially the ones that are not in the 0.8.x range.


## 2. File does not contain an SPDX Identifier

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol


## 3. Function order

Functions should be ordered following [the Solidity conventions](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions):

"Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private"

In a lot of contracts this best practice has not been followed.


## 4. Use modifiers for better readability and code reuse

To improve readability and code reuse, a modifer function can be defined instead of performing a manual conditional check within multiple affected functions.

```
	require(msg.sender == admin, "Only admin can set KYC registry");
```

## 5. `require()` should be used instead of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

```
97       assert(cashProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol
```
106      assert(cashKYCSenderProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol
```
106      assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol


## 6. Add constructor initializers

As per OpenZeppelin’s (OZ) [recommendation](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/6), “The guidelines are now to make it impossible for anyone to run initialize on an implementation contract, by adding an empty constructor with the initializer modifier. So the implementation contract gets initialized automatically upon deployment.”

Note that this behaviour is also incorporated the OZ Wizard since the UUPS vulnerability discovery: “Additionally, we modified the code generated by the Wizard 19 to include a constructor that automatically initializes the implementation when deployed.”

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L30-L51
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L30-L51


## 7. Immutable addresses lack zero-address check

Constructors should check the address written in an immutable address variable is not the zero address.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L53-L55
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L53-L55
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L53-L55


## 8. Remove commented out code

```
141	// comptroller.transferVerify(address(this), src, dst, tokens);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

