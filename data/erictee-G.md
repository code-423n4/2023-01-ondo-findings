### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contracts/cash/kyc/KYCRegistry.sol:L94      abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```

### [G-02] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/lending/tokens/cCash/CCashDelegate.sol:L15  constructor() {}

contracts/lending/tokens/cToken/CTokenDelegate.sol:L15  constructor() {}

contracts/cash/Proxy.sol:L25  ) TransparentUpgradeableProxy(_logic, _admin, _data) {}

```
     
### [G-03] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
contracts/lending/JumpRateModelV2.sol:L1      pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L7      pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L138      pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L181      pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L296      pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L302      pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L324      pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L647      pragma solidity ^0.5.12;
```

### [G-04] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/cash/CashManager.sol:L750    for (uint256 i = 0; i < size; ++i) {

contracts/cash/CashManager.sol:L786    for (uint256 i = 0; i < size; ++i) {

contracts/cash/CashManager.sol:L933    for (uint256 i = 0; i < size; ++i) {

contracts/cash/CashManager.sol:L961    for (uint256 i = 0; i < exCallData.length; ++i) {

contracts/cash/kyc/KYCRegistry.sol:L163    for (uint256 i = 0; i < length; i++) {

contracts/cash/kyc/KYCRegistry.sol:L180    for (uint256 i = 0; i < length; i++) {

contracts/cash/factory/CashKYCSenderFactory.sol:L137    for (uint256 i = 0; i < exCallData.length; ++i) {

contracts/cash/factory/CashFactory.sol:L127    for (uint256 i = 0; i < exCallData.length; ++i) {

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:L137    for (uint256 i = 0; i < exCallData.length; ++i) {

```

### [G-05] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
contracts/lending/OndoPriceOracleV2.sol:L292    require(

contracts/lending/tokens/cCash/CTokenCash.sol:L45    require(

contracts/lending/tokens/cToken/CTokenModified.sol:L45    require(

```
