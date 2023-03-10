Consider using a chainId and nonce as part of the ERC712 Permit of `addKYCAddressViaSignature` 

Description: without using a chainID and a nonce the user is suspectible to replay attacks. In two scenarios.
- replay attacks from other chain / networks as the signature can be replayed (especially without a nonce)
- If admin removesKYCAdderess after already approving the KYC (given the same message can be replayed as we're not iterating just a bool check)

Code: https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L93-L94

Mitigation:
- include nonce and chainID to the structHash

--- 
Inconsistent use of pragma:
pragma solidity 0.6.12; // @audit - old pragma, upgrade >= 0.8.X (and fixed the version no floating)
Code: https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracle.sol#L15-L16


Mitigation: fix the pragma version to >= 0.8.X

--- 

zero address check on comptroller

code: https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/compound/Comptroller.sol#L125-L126

Mitigation: check if _admin != address(0)