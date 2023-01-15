[L] Missing zero address checks

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L54
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L54
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L55-L56
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L157-L161


[NC] Avoid shadowing inherited state variables

There are some local variables that shadow inherited properties with the same name. This use causes compilers to issue warnings, negatively affecting checking and code readability.

Shadowed: `_admin`
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L25
Shadowed: `name`, `symbol`, `kycRegistry`, `kycRequirementGroup`
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L52-L53
Shadowed: `name`, `symbol`, `kycRegistry`, `kycRequirementGroup`
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L52-L53