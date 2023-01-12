

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/ondo/ondo-token/IOndo.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::2 => pragma solidity ^0.8.10;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2023-01-ondo/contracts/cash/CashManager.sol::175 => block.timestamp -
2023-01-ondo/contracts/cash/CashManager.sol::176 => (block.timestamp % epochDuration);
2023-01-ondo/contracts/cash/CashManager.sol::577 => uint256 epochDifference = (block.timestamp - currentEpochStartTimestamp) /
2023-01-ondo/contracts/cash/CashManager.sol::584 => block.timestamp -
2023-01-ondo/contracts/cash/CashManager.sol::585 => (block.timestamp % epochDuration);
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::92 => require(block.timestamp <= deadline, "KYCRegistry: signature expired");
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::294 => (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::39 => return block.timestamp > cliffTimestamp;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::44 => return block.timestamp > cliffTimestamp + seedVestingPeriod;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::78 => elapsed = block.timestamp - cliffTimestamp;
```

## [L-03] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2023-01-ondo/contracts/cash/CashManager.sol::181 => (IERC20Metadata(_cash).decimals() -
2023-01-ondo/contracts/cash/CashManager.sol::182 => IERC20Metadata(_collateral).decimals());
2023-01-ondo/contracts/lending/CompoundLens.sol::59 => underlyingDecimals = EIP20Interface(cErc20.underlying()).decimals();
2023-01-ondo/contracts/lending/CompoundLens.sol::75 => cTokenDecimals: cToken.decimals(),
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::262 => uint256(IERC20Like(underlying).decimals()) -
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::263 => uint256(AggregatorV3Interface(chainlinkOracle).decimals())));
```

## [L-04] pragma experimental ABIEncoderV2 is deprecated

Use pragma abicoder v2 instead

```
2023-01-ondo/contracts/lending/CompoundLens.sol::2 => pragma experimental ABIEncoderV2;
```

## [L-05] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2023-01-ondo/contracts/lending/CompoundLens.sol::198 => return (keccak256(abi.encodePacked((a))) ==
2023-01-ondo/contracts/lending/CompoundLens.sol::199 => keccak256(abi.encodePacked((b))));
```

## [L-06] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2023-01-ondo/contracts/cash/factory/CashFactory.sol::97 => assert(cashProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::106 => assert(cashKYCSenderProxyAdmin.owner() == guardian);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::106 => assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```

## [L-07] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::160 => token.transfer(admin, balance);
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::201 => token.transferFrom(from, address(this), amount);
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::241 => token.transfer(to, amount);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::160 => token.transfer(admin, balance);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::201 => token.transferFrom(from, address(this), amount);
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::241 => token.transfer(to, amount);
```

## [L-08] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2023-01-ondo/contracts/cash/CashManager.sol::896 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/CashManager.sol::907 => function setKYCRegistry(
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::32 => function setAssetRecipient(address _assetRecipient) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::35 => function setFeeRecipient(address _feeRecipient) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::38 => function setAssetSender(address newAssetSender) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::41 => function setMinimumDepositAmount(uint256 _minimumDepositAmount) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::44 => function setMintFee(uint256 _mintFee) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::47 => function setMintExchangeRate(
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::53 => function setMintExchangeRateDeltaLimit(
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::33 => function setKYCRequirementGroup(uint256 group) external;
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::36 => function setKYCRegistry(address registry) external;
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::34 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::40 => function setKYCRegistry(
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::34 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::40 => function setKYCRegistry(
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::28 => function setPrice(address fToken, uint256 price) external;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::30 => function setFTokenToCToken(address fToken, address cToken) external;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::32 => function setOracle(address newOracle) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::28 => function setPrice(address fToken, uint256 price) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::30 => function setFTokenToCToken(address fToken, address cToken) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::32 => function setOracle(address newOracle) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::78 => function setPriceCap(address fToken, uint256 value) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::80 => function setFTokenToChainlinkOracle(
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::85 => function setMaxChainlinkOracleTimeDelay(
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::89 => function setFTokenToOracleType(
```

## [L-09] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2023-01-ondo/contracts/cash/Proxy.sol::18 => import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";
2023-01-ondo/contracts/cash/kyc/KYCRegistryClientInitializable.sol::18 => import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";
2023-01-ondo/contracts/cash/token/Cash.sol::18 => import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::18 => import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::18 => import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::51 => * contract-specific code that enables us to report opaque error codes from upgradeable contracts.
```

## [L-24] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
2023-01-ondo/contracts/cash/kyc/KYCRegistryClientInitializable.sol::18 => import "contracts/cash/external/openzeppelin/contracts-upgradeable/proxy/Initializable.sol";
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::51 => * contract-specific code that enables us to report opaque error codes from upgradeable contracts.
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/OndoPriceOracle.sol::15 => pragma solidity 0.6.12;
2023-01-ondo/contracts/lending/ondo/ondo-token/IOndo.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::2 => pragma solidity >=0.8.3;
2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::7 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::138 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::181 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::296 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::302 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::324 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::647 => pragma solidity ^0.5.12;
2023-01-ondo/contracts/lending/tokens/cToken/CErc20.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::2 => pragma solidity ^0.8.10;
2023-01-ondo/contracts/lending/tokens/cToken/CTokenModified.sol::2 => pragma solidity ^0.8.10;
```

## [N-2] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::9 => *         `Exp({mantissa: 5100000000000000000})`.
```

## [N-3] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::76 => event FeeRecipientSet(address oldFeeRecipient, address newFeeRecipient);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::84 => event AssetRecipientSet(address oldAssetRecipient, address newAssetRecipient);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::92 => event AssetSenderSet(address oldAssetSender, address newAssetSender);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::102 => event MinimumDepositAmountSet(uint256 oldMinimum, uint256 newMinimum);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::111 => event MinimumRedeemAmountSet(uint256 oldRedeemMin, uint256 newRedeemMin);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::121 => event MintFeeSet(uint256 oldFee, uint256 newFee);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::164 => event ExchangeRateDeltaLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::189 => event MintLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::199 => event RedeemLimitSet(uint256 oldLimit, uint256 newLimit);
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::209 => event EpochDurationSet(uint256 oldDuration, uint256 newDuration);
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::48 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::66 => event CTokenOracleSet(address oldOracle, address newOracle);
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::66 => event CTokenOracleSet(address oldOracle, address newOracle);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::150 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::201 => event Mint(address minter, uint mintAmount, uint mintTokens);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::206 => event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::245 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::250 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol::432 => event NewImplementation(address oldImplementation, address newImplementation);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::135 => event Mint(address minter, uint mintAmount, uint mintTokens);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::140 => event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::179 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::184 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::366 => event NewImplementation(address oldImplementation, address newImplementation);
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol::53 => event Failure(uint error, uint info, uint detail);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::334 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::385 => event Mint(address minter, uint256 mintAmount, uint256 mintTokens);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::390 => event Redeem(address redeemer, uint256 redeemAmount, uint256 redeemTokens);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::429 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::434 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::495 => event Failure(uint256 error, uint256 info, uint256 detail);
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::616 => event NewImplementation(address oldImplementation, address newImplementation);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::148 => event KYCRegistrySet(address oldRegistry, address newRegistry);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::199 => event Mint(address minter, uint mintAmount, uint mintTokens);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::204 => event Redeem(address redeemer, uint redeemAmount, uint redeemTokens);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::243 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::248 => event NewAdmin(address oldAdmin, address newAdmin);
2023-01-ondo/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol::430 => event NewImplementation(address oldImplementation, address newImplementation);
```

## [N-4] Missing SPDX license identifier

File should include SPDX-License-Identifier

```
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/JumpRateModelV2.sol::1 => pragma solidity ^0.5.16;
2023-01-ondo/contracts/lending/tokens/cErc20ModifiedDelegator.sol::1 => /**
```

## [N-5] Missing NatSpec

Code should include NatSpec

```
2023-01-ondo/contracts/cash/Proxy.sol::1 => /**SPDX-License-Identifier: BUSL-1.1
2023-01-ondo/contracts/lending/CompoundLens.sol::1 => pragma solidity ^0.5.16;
```

## [N-6] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2023-01-ondo/contracts/cash/CashManager.sol::896 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/CashManager.sol::907 => function setKYCRegistry(
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::32 => function setAssetRecipient(address _assetRecipient) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::35 => function setFeeRecipient(address _feeRecipient) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::38 => function setAssetSender(address newAssetSender) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::41 => function setMinimumDepositAmount(uint256 _minimumDepositAmount) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::44 => function setMintFee(uint256 _mintFee) external;
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::47 => function setMintExchangeRate(
2023-01-ondo/contracts/cash/interfaces/ICashManager.sol::53 => function setMintExchangeRateDeltaLimit(
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::33 => function setKYCRequirementGroup(uint256 group) external;
2023-01-ondo/contracts/cash/interfaces/IKYCRegistryClient.sol::36 => function setKYCRegistry(address registry) external;
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::34 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::40 => function setKYCRegistry(
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::34 => function setKYCRequirementGroup(
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::40 => function setKYCRegistry(
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::28 => function setPrice(address fToken, uint256 price) external;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::30 => function setFTokenToCToken(address fToken, address cToken) external;
2023-01-ondo/contracts/lending/IOndoPriceOracle.sol::32 => function setOracle(address newOracle) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::28 => function setPrice(address fToken, uint256 price) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::30 => function setFTokenToCToken(address fToken, address cToken) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::32 => function setOracle(address newOracle) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::78 => function setPriceCap(address fToken, uint256 value) external;
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::80 => function setFTokenToChainlinkOracle(
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::85 => function setMaxChainlinkOracleTimeDelay(
2023-01-ondo/contracts/lending/IOndoPriceOracleV2.sol::89 => function setFTokenToOracleType(
```

## [N-7] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2023-01-ondo/contracts/cash/CashManager.sol::122 => bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::123 => bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");
2023-01-ondo/contracts/cash/CashManager.sol::124 => bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::36 => bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
2023-01-ondo/contracts/cash/token/Cash.sol::22 => bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
```