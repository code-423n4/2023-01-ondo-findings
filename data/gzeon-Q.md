## Low

###

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