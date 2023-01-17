# QA Report for Ondo Finance contest
## Overview
During the audit, 6 non-critical issues were found.
№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | Order of Functions | Non-Critical | 9
NC-2 | Order of Layout | Non-Critical | 13
NC-3 | Typos | Non-Critical | 7
NC-4 | Unused named return variables | Non-Critical | 1
NC-5 | Missing and extra leading underscores | Non-Critical | 3
NC-6 | Missing NatSpec | Non-Critical | 10 

## Non-Critical Risk Findings(6)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
External functions should be placed before public:
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L589-L615 

External functions should be placed before private:
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L798-L876
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L938-L968

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
Events should be placed right after state variables:
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L143
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L153
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L186-L242
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L24-L66
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L34-L66
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L94-L137

Modifiers should be placed between events and functions:
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L151
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L162
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L557
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L885

Pragma directive should be placed before import directive:
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L19
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L20
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L20

#
### NC-3. Typos
##### Instances
- [```*         `kycRequirementGroup`. In order to sucessfully call this function,```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L62) => ```successfully```
- [```function assignRoletoKYCGroup(```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L144) => ```RoleTo```
- (L205) [```* @param addresses           Array of addresses being added as elligible```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L205) => ```eligible```
- (L236) [```* @param addresses           Array of addresses being added as elligible```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L236) => ```eligible```
- [```*      BPS_DENOMINAOR (say 9999) and `mintFee` = 1,```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L429) => ```DENOMINATOR```
- [```/// @notice Error for when caller attempts to set the KYC registry refernce```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol#L38) => ```reference```
- [```* @dev Event for when a price cap is set on an fToken's underlying assset```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L95) => ```asset```

#
### NC-4. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L784-L795

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-5. Missing and extra leading underscores
##### Instances
Internal immutables should have a leading underscore:
- [```address internal immutable guardian;```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L48) 
- [```address internal immutable guardian;```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L48) 

Public constants - should not:
- [```bytes32 public constant _APPROVAL_TYPEHASH =```](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L31)

##### Recommendation
Add and remove leading underscores where needed.
#
### NC-6. Missing NatSpec
##### Description
NatSpec is missing for 10 functions in 4 contracts.
##### Instances
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L21
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L29
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L34
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L40
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L46
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L56
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L34
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L40
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L46
- https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L56 

##### Recommendation
Add NatSpec for all functions.