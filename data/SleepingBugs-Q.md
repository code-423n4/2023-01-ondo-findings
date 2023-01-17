
## Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions
### Impact 
For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments" (quote OpenZeppelin). Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts, potentially causing loss of user fund or cause the contract to malfunction completely.

See this [doc page](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) form OpenZeppelin for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

### Vulnerability Details
In the following context of the upgradeable contracts they are expected to use gaps for avoiding collision:
- `ERC20PresetMinterPauserUpgradeable`
- `Initializable`

However, none of these contracts contain storage gap. The storage gap is essential for upgradeable contract because "It allows us to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Refer to the bottom part of this [article](https://docs.openzeppelin.com/contracts/4.x/upgradeable).

If a contract inheriting from a base contract contains additional variable, then the base contract cannot be upgraded to include any additional variable, because it would overwrite the variable declared in its child contract. This greatly limits contract upgradeability.

### Github Permalinks
https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L18

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/token/CashKYCSenderReceiver.sol#L23

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/token/CashKYCSender.sol#L18

### Tools Used
Manual analysis

### Recommended Mitigation Steps
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. 
Please reference OpenZeppelin upgradeable contract templates.
```diff
+ uint256[50] private __gap;`
```



## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
`NOTE`: None of these findings where found by [Picodes. NC-1](https://gist.github.com/iFrostizz/bbbadb3d93229f60c94f01b6626c056d)

### Summary
Zero address should be checked for state variables, immutable variables. 

### Impact
Unexpected behaviors in edge cases

### Github Permalinks
These 2 are not found by the automatic tool

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/kyc/KYCRegistry.sol#L57

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/JumpRateModelV2.sol#L66

`NOTE`: These are already "found" but not at the correct line, so better don't count them, I add them to improve the review. All lines in the later 3 links in the automatic findings are L70, while real line is L54 or L57

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/factory/CashKYCSenderFactory.sol#L54

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54

https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/kyc/KYCRegistry.sol#L57

### Mitigation
Check zero address before assigning or using it
```diff
+   if (someAddress == address(0)) {
+      revert NonZeroAddressError();
+    }
```





