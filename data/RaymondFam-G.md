## Early checks
Checks via require statement in a function logic should be as early as possible to minimize gas waste in case of a revert.

For instance, the code block below can be refactored as follows:

[File: Cash.sol#L29-L40](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L29-L40)

```diff
  function _beforeTokenTransfer(
    address from,
    address to,
    uint256 amount
  ) internal override {
-    super._beforeTokenTransfer(from, to, amount);

    require(
      hasRole(TRANSFER_ROLE, _msgSender()),
      "Cash: must have TRANSFER_ROLE to transfer"
    );

+    super._beforeTokenTransfer(from, to, amount);
  }
```
All other instances entailed:

[File: CashKYCSenderReceiver.sol#L63-L66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L63-L66)

[File: CashKYCSender.sol#L63-L66](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L63-L66)

## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: CashFactory.sol#L75-L110](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L75-L110)

```diff
  function deployCash(
    string calldata name,
    string calldata ticker
-  ) external onlyGuardian returns (address, address, address) {
+  ) external onlyGuardian returns (address cashProxiedAddr, address cashProxyAdminAddr, address cashImplementationAddr) {

    [ ... ]

-    return (
+    (cashProxiedAddr, cashProxyAdminAddr, cashImplementationAddr) =
      address(cashProxied),
      address(cashProxyAdmin),
      address(cashImplementation)
    );
```
## Use `break` or `continue` in for loop 
For loop entailing large array with reverting logic should incorporate `break` or `continue` to cater for element(s) failing to get through the iteration(s). This will tremendously save gas on instances where the loop specifically fails to execute at the end of the iterations.

Here are the three instances unanimously using the same/identical function logic:

[File: CashFactory.sol#L123-L134](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L123-L134) 
[File: CashKYCSenderFactory.sol#L133-L144](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L133-L144)
[File: CashKYCSenderReceiverFactory.sol#L133-L143](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L133-L143)

```solidity
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
  }
```
## Private function with embedded modifier reduces contract size
Just as it has been implemented in the modifiers, [`checkKYC()`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L885-L888) and [`updateEpoch()`](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L557-L560) of CashManager.sol, consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: CashFactory.sol#L151-L154](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L151-L154)

```diff
+    function _onlyGuardian() private view {
+        require(msg.sender == guardian, "CashFactory: You are not the Guardian");
+    }

    modifier onlyGuardian() {
-        require(msg.sender == guardian, "CashFactory: You are not the Guardian");
+        _onlyGuardian();
        _;
    }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using ???solc --optimize --bin sourceFile.sol???. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to ??? --optimize-runs=1???. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set ???--optimize-runs??? to a high number.

```
module.exports = {
solidity: {
version: "0.8.16",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `<=` inequality instance may be refactored as follows:

[File: KYCRegistry.sol#L92](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L92)

```diff
-    require(block.timestamp <= deadline, "KYCRegistry: signature expired");
+    require(block.timestamp < deadline + 1, "KYCRegistry: signature expired");
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: KYCRegistry.sol#L163-L165](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163-L165)

```diff
-        for (uint256 i = 0; i < length; i++) {
+        for (uint256 i = 0; i < length;) {
             kycState[kycRequirementGroup][addresses[i]] = false;

+            unchecked {
+                ++i;
+            }
          }
```
## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the function below may be refactored as follows:

[File: KYCRegistry.sol#L144-L150](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144-L150)

```diff
  function assignRoletoKYCGroup(
    uint256 kycRequirementGroup,
    bytes32 role
-  ) external onlyRole(REGISTRY_ADMIN) {
+  ) external payable onlyRole(REGISTRY_ADMIN) {
    kycGroupRoles[kycRequirementGroup] = role;
    emit RoleAssignedToKYCGroup(kycRequirementGroup, role);
  }
```
## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: OndoPriceOracleV2.sol#L292-L296](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292-L296)

```solidity
    require(
      (answeredInRound >= roundId) &&
        (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
      "Chainlink oracle price is stale"
    );
```
[File: CTokenModified.sol#L45-L48](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L45-L48)

```solidity
    require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are some of the instances entailed:

[File: CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol)

```solidity
221:    Exp memory exchangeRate = Exp({mantissa: exchangeRateCurrent()});

433:    Exp memory simpleInterestFactor = mul_(

506:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});

590:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});

1027:    Exp memory exchangeRate = Exp({mantissa: exchangeRateStoredInternal()});
```