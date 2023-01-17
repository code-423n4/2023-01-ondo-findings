# 1.`transferOwnership` should be two step process
### Vulnerability details
When transferring ownership there is a risk that the ownership is transferred to an invalid address, thus causing the contract to be without a owner.
## Proof of Concept
Affected Code:-
File: CashFactory.sol [line: 96](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L96)
File: CashKYCSenderFactory.sol [line:105](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L105)
File: CashKYCSenderReceiverFactory.sol [line:105](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L105)

### Tools Used
VS Code
### Recommended Mitigation Steps
Implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.


# 2.Use `require` instead of  `assert`
### Vulnerability details
Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process’s available gas instead of returning it, as request()/revert() did.
## Proof of Concept
Affected Code:-
File: CashFactory.sol [line: 97](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L97)
File: CashKYCSenderFactory.sol [line:105](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L106)
File: CashKYCSenderReceiverFactory.sol [line:106](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106)

### Tools Used
VS Code 
### Recommended Mitigation Steps
Assertion() should be avoided even after solidity version 0.8.0, because its documentation states “The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there’s a mistake”.