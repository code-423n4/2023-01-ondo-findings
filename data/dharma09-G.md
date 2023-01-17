### [G-01] Use Immutable instead of constant for `keccak256`

Constant expressions are [re-calculated each time it is in use]([https://github.com/ethereum/solidity/issues/9232](https://github.com/ethereum/solidity/issues/9232)), costing an extra `97` gas than a constant every time they are called.

Instances include:

Mark these as  `immutable`  instead of  `constant`

*There are 14 instances of this issue:*

**Affected source code:**

```solidity
/CashManager.sol  
122: bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
123: bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
124: bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

/[KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L31)
31: bytes32 public constant _APPROVAL_TYPEHASH =keccak256("KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)");
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");

/Cash.sol
22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

/CashKYCSender.sol
25: bytes32 public constant KYC_CONFIGURER_ROLE = keccak256("KYC_CONFIGURER_ROLE");

/CashKYCSenderReceiver.sol
26: bytes32 public constant KYC_CONFIGURER_ROLE = keccak256("KYC_CONFIGURER_ROLE");

/CashFactory.sol
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/CashKYCSenderFactory.sol
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

/CashKYCSenderReceiverFactory.sol
44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
```

### [G-02] Use constants if you already know the permanent value

Use constants if you already know the permanent value. it get directly embedded in bytecode, saving `SLOAD`

*There are 3 instances of this issue:*

**Affected source code:**

```solidity
/CashManager.sol
58: uint256 public minimumDepositAmount = 10_000;
68: uint256 public exchangeRateDeltaLimit = 100;

/OndoPriceOracleV2.sol
77: uint256 public maxChainlinkOracleTimeDelay = 90000;
```

- [CashManager.sol:58](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L58)
- [OndoPriceOracleV2.sol:L77](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L77)

### [G-03] Splitting `require()` statements that use `&&` saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&The gas difference would only be realized if the revert condition is realized(met).

*There are 5 instances of this issue:*

**Affected source code:**

- [CTokenCash.sol:L45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L45)
- [CTokenModified.sol:45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L45)
- [OndoPriceOracleV2.sol:L292](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292)
- [CTokenModified.sol:45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L45)
- [CTokenCash.sol:45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L45)

### [G-04] Return statement should be unchecked

Due to the previous condition and the comment shown `// underflow already checked above, just subtract` that , the return statement can't underflow and should be unchecked.

*There are 1 instances of this issue:*

**Affected source code:**

- [CCash.sol:L224](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L224)

### [G-05] Use immutable on variables that are set and never changed after(2.1 K per variable per usage)
```solidity
/cErc20ModifiedDelegator.sol
197: string public symbol;
202: uint8 public decimals
```

### [G-06] Empty Blocks Should Emit Something or be Removed

There are function with empty blocks. These should do something like emit an event or reverting.If not it should be removed from the contract.

*There are 2 instances of this issue:*

**Affected source code:**

- [CCashDelegate.sol:15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L15)
- [CTokenDelegate.sol:15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L15)

### [G-07] Duplicated `require() /revert()` checks should be refactored to a modifier or function(instances)

*There are 1 instances of this issue:*

**Affected source code:**

```solidity
/CErc20.sol
151: require(msg.sender == admin,"cErc20::sweepToken: only admin can sweep tokens");
269: require(msg.sender == admin,"only the admin may set the comp-like delegate");
```

- [CErc20.sol:151](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L151)

**Mitigation:**

```solidity
function is_admin() private view {
        require(msg.sender == admin, "ONLY ADMIN");
   }
```