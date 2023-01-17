In the claimMint(), the user address is given as a parameter to enter and any user can call the claimMint() for another user because there are no checks if the user calling the function is the one that's supposed to get his CASH . I don't think that's a very user-friendly experience.

Line:
 https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L241


Also there is a duplicate import at lines:
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17 and 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L21
