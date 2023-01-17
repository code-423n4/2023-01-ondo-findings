### [N-01] USE A MORE RECENT VERSION OF SOLIDITY
All contracts

### [N-02] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.
While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

**contracts\cash\token\Cash.sol** 

` 22: bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");`

**contracts\cash\token\CashKYCSender.sol** 

` 26: bytes32 public constant KYC_CONFIGURER_ROLE =  keccak256("KYC_CONFIGURER_ROLE");` 

**contracts\cash\token\CashKYCSenderReceiver.sol** 

` 26: bytes32 public constant KYC_CONFIGURER_ROLE = keccak256("KYC_CONFIGURER_ROLE");` 

**contracts\cash\factory\CashFactory.sol** 
**contracts\cash\factory\CashKYCSenderFactory.sol** 
**contracts\cash\factory\CashKYCSenderReceiverFactory.sol** 

` 44: bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");` 
` 45: bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");` 

**contracts\cash\kyc\KYCRegistry.sol** 

` 31: bytes32 public constant _APPROVAL_TYPEHASH =
    keccak256(
      "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
    );
36: bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");` 

**contracts\cash\CashManager.sol** 

` 122:bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
123:  bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
 124:  bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");` 


### [N-03] THE NONREENTRANT MODIFIER SHOULD OCCUR BEFORE ALL OTHER MODIFIERS

**contracts\cash\CashManager.sol**

` 195: function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)` 

` 241: function claimMint(
    address user,
    uint256 epochToClaim
  ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) ` 

` 662: function requestRedemption(
    uint256 amountCashToRedeem
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {` 

` 949: function multiexcall(
    ExCallData[] calldata exCallData
  )
    external
    payable
    override
    nonReentrant
    onlyRole(MANAGER_ADMIN)
    whenPaused
    returns (bytes[] memory results)
  {` 



### [L-01] Nobody has access to address(0), so msg.sender == address(0) doesn’t make sense

**contracts\lending\tokens\cToken\CTokenModified.sol**

` 1090 : if (msg.sender != pendingAdmin || msg.sender == address(0)) {
      revert AcceptAdminPendingAdminCheck();
    }
` 
### [L-02] ADD ZERO ADDRESS VALIDATION IN CONSTRUCTOR 

Parameter used in constructor is used to initialize the state variable, error in these can lead to redeployment of contract.

**contracts\cash\factory\CashFactory.sol
contracts\cash\factory\CashKYCSenderFactory.sol
contracts\cash\factory\CashKYCSenderReceiverFactory.sol**

` 53: constructor(address _guardian) {
    guardian = _guardian;
  }` 

**contracts\lending\JumpRateModelV2.sol**
66: owner = owner_;

### [L-03] USE OF ASSERT() INSTEAD OF REQUIRE()

Contracts use assert() instead of require() in multiple places. This causes a Panic error on failure and prevents the use of error strings.

**contracts\cash\factory\CashFactory.sol**

` 97: assert(cashProxyAdmin.owner() == guardian);` 

**contracts\cash\factory\CashKYCSenderFactory.sol**

` 107:assert(cashKYCSenderProxyAdmin.owner() == guardian);` 

**contracts\cash\factory\CashKYCSenderReceiverFactory.sol**

` 106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);` 

### [L-04] UNSPECIFIC COMPILER VERSION PRAGMA
Avoid floating pragmas for non-library contracts.
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.
A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.
It is recommended to pin to a concrete compiler version.

**contracts\lending\tokens\cCash\CCashDelegate.sol => pragma solidity ^0.8.10;
contracts\lending\tokens\cToken\CTokenDelegate.sol => pragma solidity ^0.8.10;
contracts\lending\JumpRateModelV2.sol => pragma solidity ^0.5.16;
contracts\lending\tokens\cCash\CCash.sol => pragma solidity ^0.8.10;
contracts\lending\tokens\cToken\CErc20.sol => pragma solidity ^0.8.10;
contracts\lending\tokens\cCash\CTokenInterfacesModifiedCash.sol  => pragma solidity ^0.8.10;
contracts\lending\tokens\cToken\CTokenInterfacesModified.sol => pragma solidity ^0.8.10;
contracts\lending\tokens\cErc20ModifiedDelegator.sol pragma solidity ^0.5.12;
contracts\lending\tokens\cCash\CTokenCash.sol  pragma solidity ^0.8.10;
contracts\lending\tokens\cToken\CTokenModified.sol  pragma solidity ^0.8.10;**


### [L-05] NON-USAGE OF SPECIFIC IMPORTS
The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace. Instead, the Solidity docs recommend specifying imported symbols explicitly.

Context: all contracts

### [L-06] In CashManager.sol  function setFeeRecipient(), function setAssetRecipient and function setAssetSender() don’t check zero address values.

There are checks of zero values for variables assetRecipient, feeRecipient and assetSender in the constructor, but they are not checked in functions that set them later.

Accidental input of address(0) can lead to loss of funds sending assets or fees to a 0 address.

**contracts\cash\CashManager.sol**

` 452: function setFeeRecipient(
    address _feeRecipient
  ) external override onlyRole(MANAGER_ADMIN)
 {
    address oldFeeRecipient = feeRecipient;
    feeRecipient = _feeRecipient;
    emit FeeRecipientSet(oldFeeRecipient, _feeRecipient);
  }` 

` 465: function setAssetRecipient(
    address _assetRecipient
  ) external override onlyRole(MANAGER_ADMIN)
 {
    address oldAssetRecipient = assetRecipient;
    assetRecipient = _assetRecipient;
    emit AssetRecipientSet(oldAssetRecipient, _assetRecipient);
  }` 

` 803: function setAssetSender(
    address newAssetSender
  ) external onlyRole(MANAGER_ADMIN) 
{
    address oldAssetSender = assetSender;
    assetSender = newAssetSender;
    emit AssetSenderSet(oldAssetSender, newAssetSender);
  }` 


### [L-07] DUPLICATED REQUIRE()/REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

There are 3 places in the code with the same ` require(msg.sender == admin, …)` 
It could be refactored to an error/modifier/function with message: “Only Admin”.

**contracts\lending\tokens\cToken\CTokenModified.sol**

` 44: require(msg.sender == admin, "only admin may initialize the market");` 

` 1360: require(msg.sender == admin, "Only admin can set KYC requirement group");` 

` 1382: require(msg.sender == admin, "Only admin can set KYC registry");` 

### [L-08] WE CAN USE FUNCTIONS INSTEAD OF MODIFIERS WITH THESE FUNCTIONS INSIDE 

Also, modifiers make code more elegant but cost more than normal functions.

**contracts\cash\CashManager.sol**

 modifier updateEpoch() {
    transitionEpoch();
    _;
  }

modifier checkKYC(address account) {
    _checkKYC(account);
    _;
  }

 modifier updateEpoch() {
    transitionEpoch();
    _;
  }