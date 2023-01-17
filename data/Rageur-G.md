## GAS-1: ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow

### Description

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

### Affected file

* CashFactory.sol (Line: 127)
* CashKYCSenderFactory.sol (Line: 137)
* CashKYCSenderReceiverFactory.sol (Line: 137)
* CashManager.sol (Line: 750, 786, 933, 961)
* KYCRegistry.sol (Line: 163, 180)

## GAS-2: <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables

### Description

Using the addition operator instead of plus-equals saves gas.

### Affected file

* CashManager.sol (Line: 221, 582, 630, 649, 681, 865, 867)

## GAS-3: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* CashManager.sol (Line: 288, 371, 413, 716)
* JumpRateModelV2.sol (Line: 133)
* KYCRegistry.sol (Line: 92)
* OndoPriceOracleV2.sol (Line: 297)

## GAS-4: Empty blocks should be removed or emit something

### Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Affected file

* CCashDelegate.sol (Line: 15)
* CTokenDelegate.sol (Line: 15)
* Proxy.sol (Line: 21)

## GAS-5: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

### Affected file

* CCash.sol (Line: 179, 193, 236)
* CErc20.sol (Line: 179, 193, 236)

## GAS-6: Internal functions only called once can be inlined to save gas

### Description

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Affected file

* Cash.sol (Line: 29)
* CashKYCSender.sol (Line: 56)
* CashKYCSenderReceiver.sol (Line: 56)
* OndoPriceOracle.sol (Line: 119)
* OndoPriceOracleV2.sol (Line: 210, 251, 324)

## GAS-7: Length calculated at each iteration in For-loop

### Description

Caching the array length outside a loop saves reading it on each iteration, as long as the array’s length is not changed during the loop.

### Affected file

* CashFactory.sol (Line: 127)
* CashKYCSenderFactory.sol (Line: 137)
* CashKYCSenderReceiverFactory.sol (Line: 137)
* CashManager.sol (Line: 961)

## GAS-8: Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Description

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

### Affected file

* CTokenInterfacesModified.sol (Line: 92, 95, 108)
* CTokenInterfacesModifiedCash.sol (Line: 94, 97, 110)
* OndoPriceOracle.sol (Line: 45, 49)
* OndoPriceOracleV2.sol (Line: 55, 58, 62, 70, 73)
* cErc20ModifiedDelegator.sol (Line: 273, 278, 293)

## GAS-9: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* CashManager.sol (Line: 784)

## GAS-10: Prefix increments cheaper than Postfix increments

### Description

++i costs less gas than i++, especially when it’s used in for-loops (—i/i— too). Saves 5 gas per loop.

### Affected file

* KYCRegistry.sol (Line: 163, 180)

## GAS-11: Public functions not called by the contract should be declared external instead

### Description

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

### Affected file

* CCashDelegate.sol (Line: 21, 39)
* CTokenDelegate.sol (Line: 21, 39)
* CashKYCSender.sol (Line: 46)
* CashKYCSenderReceiver.sol (Line: 46)
* JumpRateModelV2.sol (Line: 152)
* cErc20ModifiedDelegator.sol (Line: 499)

## GAS-12: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* CashFactory.sol (Line: 151)
* CashKYCSenderFactory.sol (Line: 162)
* CashKYCSenderReceiverFactory.sol (Line: 162)
* CashManager.sol (Line: 557, 885)

## GAS-13: Require()/Revert() strings longer than 32 bytes cost extra gas

### Description

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

### Affected file

* CCash.sol (Line: 151, 155, 269)
* CCashDelegate.sol (Line: 30, 45)
* CErc20.sol (Line: 151, 155, 269)
* CTokenDelegate.sol (Line: 30, 45)
* Cash.sol (Line: 36)
* CashFactory.sol (Line: 152)
* CashKYCSender.sol (Line: 63, 70)
* CashKYCSenderFactory.sol (Line: 163)
* CashKYCSenderReceiver.sol (Line: 63, 70, 78)
* CashKYCSenderReceiverFactory.sol (Line: 163)
* JumpRateModelV2.sol (Line: 89)
* KYCRegistry.sol (Line: 87, 88)
* OndoPriceOracle.sol (Line: 120)
* OndoPriceOracleV2.sol (Line: 215, 280)
* cErc20ModifiedDelegator.sol (Line: 725, 725, 1251, 1251)

## GAS-14: Should use arguments instead of state variable

### Description

Using function's parameter cost less gas then a state variable.

### Affected file

* CashManager.sol (Line: 379, 822)
* JumpRateModelV2.sol (Line: 184)

## GAS-15: Splitting require() statements that use && saves gas

### Description

Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

### Affected file

* OndoPriceOracleV2.sol (Line: 292)

## GAS-16: Storage pointer to a structure is cheaper than copying each value of the structure into memory, same for array and mapping

### Description

It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are literally copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper.

### Affected file

* OndoPriceOracleV2.sol (Line: 284)

## GAS-17: Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

### Description

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.

### Affected file

* OndoPriceOracleV2.sol (Line: 286, 290)

## GAS-18: Use custom errors rather than revert()/require() strings to save gas

### Description

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

### Affected file

* CCash.sol (Line: 151, 155, 220, 260, 269)
* CCashDelegate.sol (Line: 30, 45)
* CErc20.sol (Line: 151, 155, 220, 260, 269)
* CTokenDelegate.sol (Line: 30, 45)
* Cash.sol (Line: 36)
* CashFactory.sol (Line: 131, 152)
* CashKYCSender.sol (Line: 63, 70)
* CashKYCSenderFactory.sol (Line: 141, 163)
* CashKYCSenderReceiver.sol (Line: 63, 70, 78)
* CashKYCSenderReceiverFactory.sol (Line: 141, 163)
* CashManager.sol (Line: 142, 145, 148, 151, 154, 206, 247, 250, 286, 289, 292, 343, 346, 372, 414, 437, 627, 643, 646, 673, 717, 759, 857, 921, 965)
* JumpRateModelV2.sol (Line: 89)
* KYCRegistry.sol (Line: 87, 88, 92)
* OndoPriceOracle.sol (Line: 120)
* OndoPriceOracleV2.sol (Line: 110, 164, 211, 215, 255, 280, 292, 297)
* cErc20ModifiedDelegator.sol (Line: 725, 725, 1251, 1251)

## GAS-19: Use require() instead of assert()

### Description

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

### Affected file

* CashFactory.sol (Line: 97)
* CashKYCSenderFactory.sol (Line: 106)
* CashKYCSenderReceiverFactory.sol (Line: 106)

## GAS-20: Using bools for storage incurs overhead

### Description

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot’s contents, replace the bits taken up by the boolean, and then write back. This is the compiler’s defense against contract upgrades and pointer aliasing, and it cannot be disabled. Use uint256(1) and uint256(2) for true/false instead.

### Affected file

* CTokenInterfacesModified.sol (Line: 15)
* CTokenInterfacesModifiedCash.sol (Line: 17)
* KYCRegistry.sol (Line: 39)
* cErc20ModifiedDelegator.sol (Line: 187, 368)

## GAS-21: Using private for constants saves gas

### Description

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

### Affected file

* CTokenInterfacesModified.sol (Line: 33, 36, 113, 164)
* CTokenInterfacesModifiedCash.sol (Line: 35, 38, 115, 166)
* Cash.sol (Line: 22)
* CashFactory.sol (Line: 44, 45, 46)
* CashKYCSender.sol (Line: 26)
* CashKYCSenderFactory.sol (Line: 44, 45, 46)
* CashKYCSenderReceiver.sol (Line: 26)
* CashKYCSenderReceiverFactory.sol (Line: 44, 45, 46)
* CashManager.sol (Line: 89, 122, 123, 124)
* JumpRateModelV2.sol (Line: 29)
* KYCRegistry.sol (Line: 31, 36)
* cErc20ModifiedDelegator.sol (Line: 208, 213, 350, 368)

## GAS-22: Variable initialized with their default value

### Description

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it’s default value costs unnecesary gas.

### Affected file

* CTokenInterfacesModifiedCash.sol (Line: 115)
* CashFactory.sol (Line: 127)
* CashKYCSenderFactory.sol (Line: 137)
* CashKYCSenderReceiverFactory.sol (Line: 137)
* CashManager.sol (Line: 64, 750, 786, 933, 961)
* KYCRegistry.sol (Line: 163, 180)

## GAS-23: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas

### Affected file

* KYCRegistry.sol (Line: 94)