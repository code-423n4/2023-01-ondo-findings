## DESCRIPTIVE TIME UNITS
As denoted in the link below:

https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html

"Suffixes like seconds, minutes, hours, days and weeks after literal numbers can be used to specify units of time where seconds are the base unit ..."

It is recommended adopting the above method when assigning variables with time units. 

Here is 1 instance found.

[OndoPriceOracleV2.sol#L77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)

```
  uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
```
Suggested fix:

```
  uint256 public maxChainlinkOracleTimeDelay = 25 hours
```
## DOS ARISING FROM LOOP WITH CHECKS
Iterative logic involving check(s) that could revert should be embedded with `continue`, `break`, and/or `return` to skip the bad element(s) in the array. This is to ensure all qualified elements in the list get to successfully finish their calls and avoid the waste of gas. Imagine a function call involving a big loop with sizable array were to revert only at the last iteration, the caller would be grieved for the wasted efforts on top of the gas cost that could be quite significant in congested hours.

Here are the 3 instances with identical logic found.

[CashKYCSenderReceiverFactory.sol#L133-L143](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L133-L143)
[CashFactory.sol#L123-L134](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L123-L134)
[CashKYCSenderFactory.sol#L133-L144](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L133-L144)

```
  function multiexcall(
    ExCallData[] calldata exCallData
  ) external payable override onlyGuardian returns (bytes[] memory results) {
    results = new bytes[](exCallData.length);
    for (uint256 i = 0; i < exCallData.length; ++i) {
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
      require(success, "Call Failed");
      results[i] = ret;
    }
```
Suggested fix:

```
  function multiexcall(
    ExCallData[] calldata exCallData
  ) external payable override onlyGuardian returns (bytes[] memory results) {
    results = new bytes[](exCallData.length);
    for (uint256 i = 0; i < exCallData.length; ++i) {
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
      if (!success) continue;
      results[i] = ret;
    }
```
## BLOCK TIME IMPLICATION
The approximate number of blocks per year that is assumed by the interest rate model is biasing to a slightly higher limit. This is because Ethereum Average Block Time is always going to be slightly greater than 12 seconds [(12.06 as at the point of this writing)](https://www.google.com/search?q=evm+block+time&rlz=1C1CHBF_enUS913US913&oq=evm+block+time&aqs=chrome..69i57j0i22i30i625j0i390l5.8140j0j7&sourceid=chrome&ie=UTF-8) due to some validators occasionally missing some slots.

Here is 1 specific instance found.

[JumpRateModelV2.sol#L26-L29](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L26-L29)

```
  /**
   * @notice The approximate number of blocks per year that is assumed by the interest rate model
   */
  uint public constant blocksPerYear = 2628000;
```
Suggested fix:

It is recommended using time units with exact seconds elapsed to eradicate the delta errors when determining [`baseRatePerBlock`, `multiplierPerBlock`, and `jumpMultiplierPerBlock`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L177-L181).

## MINTER_ROLE NOT GRANTED
The factory deploy logic is missing with granting MINTER_ROLE to the guardian.

Here are the 3 instances found.

[CashFactory.sol#L89-L90](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L89-L90) 

```
    cashProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashProxied.grantRole(PAUSER_ROLE, guardian);
```
[CashKYCSenderFactory.sol#L98-L99](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L98-L99)

```
    cashKYCSenderProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashKYCSenderProxied.grantRole(PAUSER_ROLE, guardian);
```
[CashKYCSenderReceiverFactory.sol#L98-L99](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L98-L99)

```
    cashKYCSenderReceiverProxied.grantRole(DEFAULT_ADMIN_ROLE, guardian);
    cashKYCSenderReceiverProxied.grantRole(PAUSER_ROLE, guardian);
```
Suggested fix:

It is recommended adding `grantRole(MINTER_ROLE, guardian)` to the function code block equipping the guardian with all three roles revoked by the factory contracts.  

## STORAGE GAP FOR UPGRADEABLE CONTRACTS
Consider adding a storage gap at the end of each upgradeable contract. In the event some contracts needed to inherit from them, there would not be an issue shifting down of storage in the inheritance chain. Generally, storage gaps are a novel way of reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts. If not, the variable in the child contract might be overridden whenever new variables are added to it. This storage collision could have unintended and vulnerable consequences to the child contracts.

Here are the 3 contract instances found.

[Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)
[CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
[CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

Suggested fix:

It is recommended adding the following code block at the end of the upgradeable contract:

```
    /**
     * @dev This empty reserved space is put in place to allow future versions to add new
     * variables without shifting down storage in the inheritance chain.
     * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
     */
    uint256[49] private __gap;
```
## SANITY CHECK THAT COULD SILENTLY FAIL
A sanity check using `totalSupply()` was applied on the state variable, `underlying`, when initializing the money market in CCash.sol and CErc20.sol.

Here is the identical logic associated with the 2 instances found.

[CCash.sol#L53-L55](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L53-L55)
[CErc20.sol#L53-L55](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L53-L55)

```
    // Set underlying and sanity check it
    underlying = underlying_;
    EIP20Interface(underlying).totalSupply();
```
Depending on the EIP20Interface instance used, `totalSupply()` might not revert on the wrong input address of `underlying_`.

Suggested fix:

It is recommended making a check to ensure `EIP20Interface(underlying).totalSupply() > 0`. 