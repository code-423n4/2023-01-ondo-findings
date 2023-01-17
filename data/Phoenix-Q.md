### Need to check for address(0), before setting the `guardian` on `CashFactory`

The ´constructor` of the contracts should check if `_guardian` against `address(0)` before setting the `guardian`. There has been many  severe impacts in the past of DeFi that proves we should check constrains even when the deployer is expected to be secure/reliable.

[CashFactory.sol#L54](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L54`)

[CashKYCSenderFactory.sol#L53](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L53`)

[CashKYCSenderReceiverFactory.sol#L53](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L53`)

The ´constructor` of the contract `KYCRegistry` should check `admin` against `address(0)`

[KYCRegistry.sol#L51](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L51`)

The `constructor` of `KYCRegistryClientConstructable` should check `_kycRegistry` against `address(0)`

[KYCRegistryClientConstructable.sol#L36](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L36`)

The functions `__KYCRegistryClientInitializable_init` and `__KYCRegistryClientInitializable_init_unchained`  of `KYCRegistryClientInitializable ` should check `_kycRegistry` against `address(0)`

[KYCRegistryClientInitializable.sol#L44-L58](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L44-L58`)

### Contracts making `multiexcall` should be whitelisted
Making many external calls through iteration, even after `onlyGuardian` validation, opens a attack vector with external contracts. To make the calls less risky one should whilelist the contracts allowed to make those calls.

[CashFactory.sol#L123](` https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L123`)

### The `initialize` functions above are susceptible to front run attack. They should be constrained by `onlyOwner` or `onlyGuardian` pattern, where a deployer is set at the `constructor` and checked at the begining of the `initialize` function

[CashKYCSender.sol#L46](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L46`)

[CashKYCSenderReceiver.sol#L46](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L46`)

[CCash.sol#L30](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L30`)

[CErc20.sol#L30](`https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L30`)

