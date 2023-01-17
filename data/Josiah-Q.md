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