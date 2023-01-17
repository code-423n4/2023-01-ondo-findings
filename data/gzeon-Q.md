## Low

### KYC can be removed while user still have fund in the protocol

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L169-L184

```solidity
  /**
   * @notice Remove addresses from KYC list
   *
   * @param kycRequirementGroup KYC group associated with `addresses`
   * @param addresses           List of addresses to revoke KYC'd status
   */
  function removeKYCAddresses(
    uint256 kycRequirementGroup,
    address[] calldata addresses
  ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {
    uint256 length = addresses.length;
    for (uint256 i = 0; i < length; i++) {
      kycState[kycRequirementGroup][addresses[i]] = false;
    }
    emit KYCAddressesRemoved(msg.sender, kycRequirementGroup, addresses);
  }
```

Since function like claimMint and transfer require KYC, user fund might be stuck

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L241-L244

```solidity
  function claimMint(
    address user,
    uint256 epochToClaim
  ) external override updateEpoch nonReentrant whenNotPaused checkKYC(user) {
```

## Non-Critical

### KYCRegistry does not check if address is already in the list

It is possible to add an already existing address or remove a non-existing address, emitting confusing events.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L152-L184

```solidity
  /**
   * @notice Add addresses to KYC list for specified `kycRequirementGroup`
   *
   * @param kycRequirementGroup KYC group associated with `addresses`
   * @param addresses           List of addresses to grant KYC'd status
   */
  function addKYCAddresses(
    uint256 kycRequirementGroup,
    address[] calldata addresses
  ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {
    uint256 length = addresses.length;
    for (uint256 i = 0; i < length; i++) {
      kycState[kycRequirementGroup][addresses[i]] = true;
    }
    emit KYCAddressesAdded(msg.sender, kycRequirementGroup, addresses);
  }

  /**
   * @notice Remove addresses from KYC list
   *
   * @param kycRequirementGroup KYC group associated with `addresses`
   * @param addresses           List of addresses to revoke KYC'd status
   */
  function removeKYCAddresses(
    uint256 kycRequirementGroup,
    address[] calldata addresses
  ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {
    uint256 length = addresses.length;
    for (uint256 i = 0; i < length; i++) {
      kycState[kycRequirementGroup][addresses[i]] = false;
    }
    emit KYCAddressesRemoved(msg.sender, kycRequirementGroup, addresses);
  }
```

### Manager can steal fund from the contract

This allow manager to execute arbitrary call, including transferring token in the contract.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L962-L964

```solidity
      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
```