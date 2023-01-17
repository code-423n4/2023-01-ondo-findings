# Report
## Low Risk ##
### [L-1]: Critical changes should use two-step procedure
**Context:**

1. ```function setFeeRecipient(``` [L452](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L452) 
1. ```function setAssetRecipient(``` [L465](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L465) 
1. ```function setAssetSender(``` [L803](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L803) 
1. ```function _setKYCRegistry(address _kycRegistry) internal {``` [L39](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L39) 

**Recommendation:**

The best practice is to use two-step procedure for critical changes to make them less error-prone. 

## Non-Critical Issues ##
### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return totalCashAmountRefunded;``` [L795](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L795) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Use of immutable instead of constant keccak expression
**Context:**

1. ```bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");``` [L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22) 
1. ```keccak256("KYC_CONFIGURER_ROLE");``` [L27](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L27) 
1. ```keccak256("KYC_CONFIGURER_ROLE");``` [L27](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L27) 
1. ```bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");``` [L44](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L44) 
1. ```bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");``` [L45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L45) 
1. ```bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");``` [L44](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L44) 
1. ```bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");``` [L45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L45) 
1. ```keccak256(``` [L32](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L32) 
1. ```bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");``` [L122](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L122) 
1. ```bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");``` [L123](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L123) 
1. ```bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");``` [L124](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L124) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#constant-and-immutable-state-variables) for a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. It is recommended to use immutable instead. 

### [N-3]: No same value input check
**Context:**

1. ```exchangeRateDeltaLimit = _exchangeRateDeltaLimit;``` [L396](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L396) 
1. ```mintFee = _mintFee;``` [L417](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L417) 
1. ```minimumDepositAmount = _minimumDepositAmount;``` [L440](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L440) 
1. ```feeRecipient = _feeRecipient;``` [L456](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L456) 
1. ```epochDuration = _epochDuration;``` [L550](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L550) 
1. ```mintLimit = _mintLimit;``` [L598](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L598) 
1. ```redeemLimit = _redeemLimit;``` [L613](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L613) 
1. ```assetSender = newAssetSender;``` [L807](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L807) 
1. ```minimumRedeemAmount = newRedeemMinimum;``` [L821](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L821) 
1. ```kycRegistry = IKYCRegistry(_kycRegistry);``` [L44](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L44) 
1. ```kycRequirementGroup = _kycRequirementGroup;``` [L56](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L56) 

**Recommendation:**

Example how to fix ```require(_newOwner != owner, " Same address");```

### [N-4]: Wrong order of functions
**Context:**

1. ```event CashDeployed(``` [L143](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L143) (event definition can not go after external function)
1. ```event CashKYCSenderDeployed(``` [L153](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L153) (event definition can not go after external function)
1. ```function assignRoletoKYCGroup(``` [L144](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144) (external function can not go after external view function)
1. ```pragma solidity 0.8.16;``` [L19](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L19) (pragma directive can not go after import directive)
1. ```pragma solidity 0.8.16;``` [L20](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L20) (pragma directive can not go after import directive)
1. ```pragma solidity 0.8.16;``` [L20](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L20) (pragma directive can not go after import directive)
1. ```event FTokenToCTokenSet(``` [L41](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L41) (event definition can not go after external function)
1. ```event FTokenToCTokenSet(``` [L41](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L41) (event definition can not go after external function)
1. ```event PriceCapSet(``` [L101](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L101) (event definition can not go after external function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-5]: NatSpec is missing
**Context:**

1. [Proxy.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol) 
1. [Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)
1. [CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)
1. [CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

### [N-6]: Typos
**Context:**

1. ```*         `kycRequirementGroup`. In order to sucessfully call this function,``` [L62](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L62) (Change **sucessfully** to **successfully**)
1. ```* @param addresses           Array of addresses being added as elligible``` [L205](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L205) (Change **elligible** to **eligible**)
1. ```* @param addresses           Array of addresses being added as elligible``` [L236](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L236) (Change **elligible** to **eligible**)
1. ```/// @notice Error for when caller attempts to set the KYC registry refernce``` [L38](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol#L38) (Change **refernce** to **reference**)
