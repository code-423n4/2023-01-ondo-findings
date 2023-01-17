- In `KYCRegistry.removeKYCAddresses` `delete` keyword should be used.
    ```solidity
      function removeKYCAddresses(uint256 kycRequirementGroup, address[] calldata addresses) external onlyRole(kycGroupRoles[kycRequirementGroup]) {
        uint256 length = addresses.length;
        for (uint256 i = 0; i < length; i++) {
          kycState[kycRequirementGroup][addresses[i]] = false;
        }
        emit KYCAddressesRemoved(msg.sender, kycRequirementGroup, addresses);
      }
    ```
- `CashManager.constructor` - setting `currentEpoch` is a no-op
    ```solidity
        currentEpoch = currentEpoch;
    ```
- `OndoPriceOracleV2.cTokenOracle` should be marked as `immutable`.
    ```solidity
      CTokenOracle public cTokenOracle =
        CTokenOracle(0x65c816077C29b557BEE980ae3cC2dCE80204A0C5);
    ```
