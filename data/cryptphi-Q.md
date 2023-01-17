1. Shadowed variable
local variable _admin in TokenProxy.constructor() shadows function _admin() in TransparentUpgradeableProxy._admin()

2.. Missing zero address check
a. The constructor in KYCRegistry.sol is missing a zero address check to ensure the `DEFAULT_ADMIN_ROLE` and `REGISTRY_ADMIN` roles are not set to address(0), which could lead to the KYCRegistry contract lose the ability to grant roles for KYCGroupRoles for adding addresses to KYC list
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L51-L56

b.CashFactory constructor is missing a zero address check to ensure the governor is not set to address(0) which could lead to the contract not able to deployCash. https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L53-L55

c. cCash.initialize() is missing a zero address check for `underlying_` variable to the state variable `underlying` is not set to address(0). 
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L31-L55
