| Index | Issue                                                                                    |
|-------|------------------------------------------------------------------------------------------|
| 1     | USE UNCHECK BLOCK FOR OPERATION THAT CAN NOT OVERFLOW/UNDERFLOW                          |
| 2     | Using calldata instead of memory for read-only arguments in external functions saves gas |
| 3     | Functions guaranteed to revert when called by normal users can be marked payable         |
| 4     | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead                     |
| 5     | Use functions instead of modifiers                                                       |
| 6     | splitting && statement in require function and writing them separately saves gas         |

## 1.USE UNCHECK BLOCK FOR OPERATION THAT CAN NOT OVERFLOW/UNDERFLOW
For example use uncheck block for i++ in for loop because it won't overflow/underflow
```solidity
for (uint256 i = 0; i < length; i++) {
```
[KYCRegistry.sol#L163](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163)
[KYCRegistry.sol#L180](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180)

## 2.Using calldata instead of memory for read-only arguments in external functions saves gas
```solidity
function multiexcall(
    ExCallData[] calldata exCallData
  )
    external
    payable
    override
    nonReentrant
    onlyRole(MANAGER_ADMIN)
    whenPaused
    returns (bytes[] memory results)
```
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

[CashManager.sol#L958](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L958)
[CashFactory.sol#L125](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L125)
[CashKYCSenderFactory.sol#L135](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L135)
[CashKYCSenderReceiverFactory.sol#L135](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L135)

## 3.Functions guaranteed to revert when called by normal users can be marked payable
```solidity
  function setMintExchangeRate(
    uint256 exchangeRate,
    uint256 epochToSet
  ) external override updateEpoch onlyRole(SETTER_ADMIN) {
```
a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
[CashManager.sol#L284](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L284)
[CashManager.sol#L341](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L341)
[CashManager.sol#L370](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L370)
[CashManager.sol#L394](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L394)
[CashManager.sol#L412](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L412)
[CashManager.sol#L435](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L435)
[CashManager.sol#L454](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L454)
[CashManager.sol#L467](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L467)
[CashManager.sol#L548](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L548)
[CashManager.sol#L596](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L596)
[CashManager.sol#L713](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L713)
[CashManager.sol#L805](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L805)

## 4.Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
```solidity
    uint baseRatePerBlock,
    uint multiplierPerBlock,
    uint jumpMultiplierPerBlock,
    uint kink
```
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[JumpRateModelV2.sol#L15-L18](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L15-L18)
[JumpRateModelV2.sol#L29](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29)
[JumpRateModelV2.sol#L34](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L34)
[JumpRateModelV2.sol#L39](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L39)
[JumpRateModelV2.sol#L44](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L44)
[JumpRateModelV2.sol#L49](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L49)
[JumpRateModelV2.sol#L60-L63](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L60-L63)
[JumpRateModelV2.sol#L84-L87](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L84-L87)
[JumpRateModelV2.sol#L107-L109](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L107-L109)
[JumpRateModelV2.sol#L127-L129](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L127-L129)
[JumpRateModelV2.sol#L153-L156](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L153-L156)
[JumpRateModelV2.sol#L172-L175](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L172-L175)

## 5.Use functions instead of modifiers
```solidity
  modifier updateEpoch() {
    transitionEpoch();
    _;
  }
```
use functions instead of modifiers if possible because it saves gas

[CashManager.sol#L557)](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L557)
[CashManager.sol#L885](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L885)
[CashFactory.sol#L151](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L151)
[CashKYCSenderFactory.sol#L162](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L162)
[CashKYCSenderReceiverFactory.sol#L162](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L162)

## 6.splitting && statement in require function and writing them separately saves gas    
```solidity
    require(
      accrualBlockNumber == 0 && borrowIndex == 0,
      "market may only be initialized once"
    );
```         
[CTokenCash.sol#L46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L46)
[CTokenModified.sol#L46)](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L46)