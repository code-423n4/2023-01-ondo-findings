
### Stop Using v==28 || v == 28

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L87

precompile actually checks if the value is 27 or 28. No need to do this when verify signature on the caller side

