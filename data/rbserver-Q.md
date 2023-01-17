## [01] USERS ARE UNABLE TO CALL `KYCRegistry.addKYCAddressViaSignature` FUNCTION WITH VALID SIGNATURES, WHICH HAVE `v` BEING 0 OR 1, THAT ARE SIGNED BY USING `web3.eth.sign`
The following `KYCRegistry.addKYCAddressViaSignature` function checks if the `v` input is 27 or 28; if not, calling it will revert. However, a valid signature's `v` can be 0 or 1 when it is signed by using `web3.eth.sign`, which can be confirmed by the notes and examples in https://github.com/web3/web3.js/blob/0.20.7/DOCUMENTATION.md#web3ethsign and https://web3js.readthedocs.io/en/v1.8.1/web3-eth.html#sign. Because of the current `v` requirement in the `KYCRegistry.addKYCAddressViaSignature` function, users are unable to use these valid signatures, which have `v` being 0 or 1, that are signed by using `web3.eth.sign`. This limits the protocol's usability and degrades the user experience.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L79-L112
```solidity
  function addKYCAddressViaSignature(
    uint256 kycRequirementGroup,
    address user,
    uint256 deadline,
    uint8 v,
    bytes32 r,
    bytes32 s
  ) external {
    require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
    ...
  }
```

As a mitigation, https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L87 can be updated to the following code.
```solidity
    if (v <= 1) {
        v += 27;
    }
    require(v == 27 || v == 28, "KYCRegistry: invalid v value in signature");
```

## [02] USER WORKFLOW IN DOCUMENTATION DOES NOT MATCH `CashManager.requestMint` FUNCTION CODE
The documentation in https://github.com/code-423n4/2023-01-ondo#cashmanager says that "to mint Cash tokens, a user must send USDC to the contract and call requestMint." However, as shown by the following `CashManager.requestMint` function, USDC are actually transferred from `msg.sender`, who is the user, to the `feeRecipient` and `assetRecipient` addresses directly. If the documentation is followed, the user would transfer a specified amount of USDC to the `CashManager` contract first and then call the `CashManager.requestMint` function, which would result in double spending her or his USDC. As a mitigation, this documentation needs to be updated to match the `CashManager.requestMint` function code when presenting CASH Protocol in production to users.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L195-L230
```solidity
  function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
    ...

    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);
    collateral.safeTransferFrom(
      msg.sender,
      assetRecipient,
      depositValueAfterFees
    );

    ...
  }
```

## [03] NEWER VERSION OF SOLIDITY CAN BE USED FOR `OndoPriceOracle` CONTRACT
As shown by the following code, the `OndoPriceOracle` contract uses Solidity v0.6.12. Yet, the protocol can benefit from more gas-efficient features, such as custom errors starting from Solidity v0.8.4, by using a newer version of Solidity. Hence, please consider using a newer Solidity version for this contract.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L15
```solidity
pragma solidity 0.6.12;
```

## [04] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
( Please note that the following instances are not found in https://gist.github.com/iFrostizz/bbbadb3d93229f60c94f01b6626c056d#nc-1-missing-checks-for-address0-when-assigning-values-to-address-state-variables. )

To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

`address(0)` checks are missing for `managerAdmin` and `pauser` in the following constructor. Please consider checking these.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L127-L183
```solidity
  constructor(
    address _collateral,
    address _cash,
    address managerAdmin,
    address pauser,
    address _assetRecipient,
    address _assetSender,
    address _feeRecipient,
    uint256 _mintLimit,
    uint256 _redeemLimit,
    uint256 _epochDuration,
    address _kycRegistry,
    uint256 _kycRequirementGroup
  ) KYCRegistryClientConstructable(_kycRegistry, _kycRequirementGroup) {
    ...
  }
```

`address(0)` check is missing for `_feeRecipient` in the following `setFeeRecipient` function. Please consider checking it.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L452-L458
```solidity
  function setFeeRecipient(
    address _feeRecipient
  ) external override onlyRole(MANAGER_ADMIN) {
    address oldFeeRecipient = feeRecipient;
    feeRecipient = _feeRecipient;
    emit FeeRecipientSet(oldFeeRecipient, _feeRecipient);
  }
```

`address(0)` check is missing for `_assetRecipient` in the following `setAssetRecipient` function. Please consider checking it.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L465-L471
```solidity
  function setAssetRecipient(
    address _assetRecipient
  ) external override onlyRole(MANAGER_ADMIN) {
    address oldAssetRecipient = assetRecipient;
    assetRecipient = _assetRecipient;
    emit AssetRecipientSet(oldAssetRecipient, _assetRecipient);
  }
```

`address(0)` checks are missing for `admin` and `_sanctionsList` in the following constructor. Please consider checking these.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L51-L58
```solidity
  constructor(
    address admin,
    address _sanctionsList
  ) EIP712("OndoKYCRegistry", "1") {
    _grantRole(DEFAULT_ADMIN_ROLE, admin);
    _grantRole(REGISTRY_ADMIN, admin);
    sanctionsList = ISanctionsList(_sanctionsList);
  }
```

## [05] CONSTANT CAN BE USED INSTEAD OF MAGIC NUMBER
( Please note that the following instance is not found in https://gist.github.com/iFrostizz/bbbadb3d93229f60c94f01b6626c056d#nc-4-constants-should-be-defined-rather-than-using-magic-numbers. )

To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the following magic number, which is `1e6`, with a constant in the following `_getMintAmountForEpoch` function.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L487-L493
```solidity
  function _getMintAmountForEpoch(
    uint256 collateralAmountIn,
    uint256 epoch
  ) private view returns (uint256 cashAmountOut) {
    uint256 amountE24 = _scaleUp(collateralAmountIn) * 1e6;
    cashAmountOut = amountE24 / epochToExchangeRate[epoch];
  }
```

## [06] REDUNDANT RETURN STATEMENT WHEN CORRESPONDING NAMED RETURN IS USED
When a function has a return statement while using a corresponding named return, this return statement becomes redundant. For better readability and maintainability, `return totalCashAmountRefunded;` can be removed while keeping the corresponding named return in the following `_processRefund` function.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L781-L796
```solidity
  function _processRefund(
    address[] calldata refundees,
    uint256 epochToService
  ) private returns (uint256 totalCashAmountRefunded) {
    uint256 size = refundees.length;
    for (uint256 i = 0; i < size; ++i) {
      address refundee = refundees[i];
      uint256 cashAmountBurned = redemptionInfoPerEpoch[epochToService]
        .addressToBurnAmt[refundee];
      redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;
      cash.mint(refundee, cashAmountBurned);
      totalCashAmountRefunded += cashAmountBurned;
      emit RefundIssued(refundee, cashAmountBurned, epochToService);
    }
    return totalCashAmountRefunded;
  }
```






## [07] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. The `@param` and/or `@return` comments are missing for the following functions. Please consider completing the NatSpec comments for them.
```solidity
contracts\cash\CashManager.sol
  514: function _scaleUp(uint256 amount) private view returns (uint256) {
  949: function multiexcall(

contracts\cash\kyc\KYCRegistry.sol
  128: function getKYCStatus(

contracts\cash\kyc\KYCRegistryClientInitializable.sol
  58: function __KYCRegistryClientInitializable_init_unchained(
```

## [08] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```solidity
contracts\cash\token\Cash.sol
  29: function _beforeTokenTransfer(  

contracts\cash\token\CashKYCSender.sol
  46: function initialize(  

contracts\cash\token\CashKYCSenderReceiver.sol
  46: function initialize(  
```