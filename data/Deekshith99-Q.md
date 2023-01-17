# QA Report

## [NC-1] Unused imports
There are 2 instance of it & here is the permaliks of it [CTokenInterfacesModifiedCash.sol#L7](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L7) & [CTokenInterfacesModified.sol#L7](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L7)
it is recommend to remove unused imports for better readability of the code 

## [NC-2] License type is missing
There are 2 instances of it they are [JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol) & [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol)
Its highly recommend to write the license identifier keyword (license type of the contract) above the version

## [NC-3] Use a more recent version of solidity 
It is recommend to update the solidity version according some of the contracts are still using older versions
and Versions must be consistent with each other.

## [NC-4] Stop using `v != 27 && v != 28 or v == 27 || v == 28`
Here is the permalink of it [KYCRegistry.sol#L87](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L87)
for more refer this [link](https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg)

## [NC-5] Can make it more readable
Here is the permalink of it [OndoPriceOracleV2.sol#L77](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L77)
here it can be written as `uint256 public maxChainlinkOracleTimeDelay = 90_000;` or `uint256 public maxChainlinkOracleTimeDelay = 25*60*60;`

