## Gas Optimization

| G-O    |Issue| 
|:------:|:----|
| [G&#x2011;01] | Remove public visibility from constant variables | 2 |
| [G&#x2011;02] | Cache Array Length Outside of Loop | 6 |
| [G&#x2011;03] | x = x - y costs less gas than x -= y, same for addition | 11 |
| [G&#x2011;04] | USE REQUIRE INSTEAD OF ASSERT| 19 |
| [G&#x2011;05] | Consider using custom errors instead of require statements with string error | 31 |
| [G&#x2011;06] | REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS | 2 |
| [G&#x2011;07] | THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES | 1 |
| [G&#x2011;08] | USE CALLDATA INSTEAD OF MEMORY | 1 |

Total: 8 issues

### [G-01] Remove public visibility from constant variables

Constant variables are custom to the contract and won't need to be read on-chain - anyone can just see their values from the source code and, if needed, hardcode them into other contracts. Removing the public visibility will optimise deployment cost since no automatically generated getters will exist in the bytecode of the contract.

Files: `CashFactory.sol`

```solidity
bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L44
- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L45
- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L46

### [G-02]  Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

File: `CashFactory.sol`

```solidity
for (uint256 i = 0; i < exCallData.length; ++i) {
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/factory/CashFactory.sol#L127

### [G-03]  x = x - y costs less gas than x -= y, same for addition

There are 9 instances of this problem in `CashManager.sol`
You can replace all -= and += occurrences to save gas

Here is an example of couple of them:

```solidity
mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
```

```solidity
currentEpoch += epochDifference;
```

```solidity
currentMintAmount += collateralAmountIn;
```

### [G-04] USE REQUIRE INSTEAD OF ASSERT

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

File: `CashFactory.sol`

```solidity
assert(cashProxyAdmin.owner() == guardian);
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/83a45fa7cbc427a62ccf04d0e5cf9b0e780ec26c/contracts/cash/factory/CashFactory.sol#L97

### [G-05] Consider using custom errors instead of require statements with string error

Custom errors reduce the contract size and can provide easier integration with a protocol. Consider using those instead of require statements with string errors

Couple of examples:

File: `cErc20ModifiedDelegator.sol`

```solidity
require(msg.sender == admin,
"CErc20Delegator::_setImplementation: Caller must be admin");
```

File: `CTokenModified.sol`

```solidity
require(msg.sender == admin, "Only admin can set KYC requirement group");
```

### [G-06] REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

File: `CashFactory.sol`

```solidity
require(msg.sender == guardian, "CashFactory: You are not the Guardian");
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L152

File: `JumpRateModelV2.sol`

```solidity
require(msg.sender == owner, "only the owner may call this function.");
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L89

### [G-07] THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES

If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

File: `CashManager.sol`

```solidity
 uint256 public mintFee = 0;
```

Lines of code: 

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L64

File: `CTokenInterfacesModifiedCash.sol`

```solidity
uint public constant protocolSeizeShareMantissa = 0;
```

Lines of code: 

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115

### [G-08] USE CALLDATA INSTEAD OF MEMORY

Some methods are declared as external but the arguments are defined as memory instead of as calldata.
By marking the function as external it is possible to use calldata in the arguments shown below and save significant gas.

File: `CTokenInterfacesModifiedCash.sol`

```solidity
function _setImplementation(
    address implementation_,
    bool allowResign,
    bytes memory becomeImplementationData
  ) external virtual;
```

```solidity
function _becomeImplementation(bytes memory data) external virtual;
```

Lines of code:

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L443

- https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L453