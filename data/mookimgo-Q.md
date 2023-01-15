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

# sanctionsList in KYCRegistry should also be set as a constant 0x40C57923924B5c5c5455c48D93317139ADDaC8fb

As cErc20ModifiedDelegator.sol has used this address constant:

```
  ISanctionsList public constant sanctionsList =
    ISanctionsList(0x40C57923924B5c5c5455c48D93317139ADDaC8fb);
````

There seems no needs to set a constructor parameter, suggest changing https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L48-L49 to above constant define and remove constructor parameter `_sanctionsList `.