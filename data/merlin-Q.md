# Non-critical
## Issues with imports

- abstract contract KYCRegistryClientConstructable has two identical imports:
[KYCRegistryClientConstructable.sol#L17-L21](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17-L21)
- abstract contract KYCRegistryClient has IKYCRegistryClient.sol and IKYCRegistry.sol imports, but IKYCRegistryClient interface has import IKYCRegistry.sol:
[KYCRegistryClient.sol#L17](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L17)
Need to remove IKYCRegistry import from KYCRegistryClient smart contract.
- useless import "contracts/cash/external/openzeppelin/contracts/token/IERC20.sol"; cause IERC20Metadata has already IERC20.sol import
[CashManager.sol#L22](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L22)

## Fix typos in documentation

KYCRegistryClient smart contract has title of KYCRegistryClientInitializable in natspec documentation: [KYCRegistryClient.sol#L23](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L23)

# Low
## KYC can be approved to user from sanctions list

The addKYCAddressViaSignature, addKYCAddresses functions do not check whether the user's address is on the sanction list:
- [KYCRegistry.sol#L79-L112](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L79-L112)
- [KYCRegistry.sol#L158-L167](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L158-L167)