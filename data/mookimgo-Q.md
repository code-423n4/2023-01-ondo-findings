# event KYCAddressAddViaSignature should mark kycRequirementGroup as indexed

```
  event KYCAddressAddViaSignature(
    address indexed sender,
    address indexed user,
    address indexed signer,
    uint256 kycRequirementGroup,
    uint256 deadline
  );

  event KYCAddressesAdded(
    address indexed sender,
    uint256 indexed kycRequirementGroup,
    address[] addresses
  );
```

As shown above, event KYCAddressesAdded has kycRequirementGroup indexed, but KYCAddressAddViaSignature don't

This will make log search using eth_getLogs by kycRequirementGroup infeasible.

Suggest: remove sender index and add kycRequirementGroup index

# event KYCAddressAddViaSignature, KYCAddressesAdded, and KYCAddressesRemoved should move kycRequirementGroup to the first indexed argument

Considering we want to search KYC'ed users for a specific kycRequirementGroup.

Using eth_getLogs will can not do that, as it can only specify `topics0, topics1, ...` in order (cannot search by topics2 without topics1), and in current design, user is placed before kycRequirementGroup.

Suggest: change to:

```
  event KYCAddressAddViaSignature(
    uint256 indexed kycRequirementGroup,
    address indexed user,
    address indexed signer,
    address sender,
    uint256 deadline
  );

  event KYCAddressesAdded(
    uint256 indexed kycRequirementGroup,
    address indexed sender,
    address[] addresses
  );

  event KYCAddressesRemoved(
    uint256 indexed kycRequirementGroup,
    address indexed sender,
    address[] addresses
  );
```