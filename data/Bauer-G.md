## 1.addKYCAddresses() and removeKYCAddresses() can be combined into one to save gas
KYCRegister.sol
Function call addKYCAddresses() and removeKYCAddresses() can be combined into one to save gas

```
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
## Recommended Mitigation Steps：
```
function updateKYCAddresses(
    uint256 kycRequirementGroup,
    address[] calldata addresses,
   bool v,
  ) external onlyRole(kycGroupRoles[kycRequirementGroup]) {
    uint256 length = addresses.length;
    for (uint256 i = 0; i < length; i++) {
      kycState[kycRequirementGroup][addresses[i]] = v;
    }
    emit KYCAddressesAdded(msg.sender, kycRequirementGroup, addresses);
  }

```