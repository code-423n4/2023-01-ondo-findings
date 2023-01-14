## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 1 | 100 |
| [GAS&#x2011;2](#GAS&#x2011;2) | State variables only set in the `constructor` should be declared immutable | 1 | - |
| [GAS&#x2011;3](#GAS&#x2011;3) | Setting the `constructor` to `payable` | 13 | 169 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Do not calculate constants | 2 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 5 | 140 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Using `delete` statement can save gas | 7 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Use `assembly` to write address storage values | 10 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | `internal` functions only called once can be inlined to save gas | 47 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 18 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Optimize names to save gas | 18 | 396 |
| [GAS&#x2011;11](#GAS&#x2011;11) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 9 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Public Functions To External | 36 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | `require()` Should Be Used Instead Of `assert()` | 3 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 5 | - |
| [GAS&#x2011;15](#GAS&#x2011;15) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 9 | 315 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Using `unchecked` blocks to save gas | 16 | 2176 |
| [GAS&#x2011;17](#GAS&#x2011;17) | Use solidity version 0.8.17 to gain some gas boost | 31 | 2728 |
| [GAS&#x2011;18](#GAS&#x2011;18) | Using `storage` instead of `memory` saves gas | 1 | 350 |

Total: 422 contexts over 31 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
94: abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistry.sol#L94








### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> State variables only set in the `constructor` should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

#### <ins>Proof Of Concept</ins>

```solidity
66: owner = owner_
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L66





### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
127: constructor(
    address _collateral,
    address _cash,
    address managerAdmin,
    address pauser,
    address _assetRecipient,
    address _assetSender,
    address _feeRecipient,
    uint256 _mintLimit,
    uint256 _redeemLimit,
    uint256 _epochDuration,
    address _kycRegistry,
    uint256 _kycRequirementGroup
  ) KYCRegistryClientConstructable(_kycRegistry, _kycRequirementGroup)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L127

```solidity
21: constructor(
    address _logic,
    address _admin,
    bytes memory _data
  ) TransparentUpgradeableProxy(_logic, _admin, _data)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\Proxy.sol#L21

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashFactory.sol#L53

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderFactory.sol#L53

```solidity
53: constructor(address _guardian)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderReceiverFactory.sol#L53

```solidity
51: constructor(
    address admin,
    address _sanctionsList
  ) EIP712("OndoKYCRegistry", "1")
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistry.sol#L51

```solidity
36: constructor(address _kycRegistry, uint256 _kycRequirementGroup)
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientConstructable.sol#L36

```solidity
25: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\Cash.sol#L25

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSender.sol#L30

```solidity
30: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSenderReceiver.sol#L30

```solidity
59: constructor(
    uint baseRatePerYear,
    uint multiplierPerYear,
    uint jumpMultiplierPerYear,
    uint kink_,
    address owner_
  ) public
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L59

```solidity
15: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCashDelegate.sol#L15

```solidity
15: constructor()
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenDelegate.sol#L15





### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
115: uint public constant protocolSeizeShareMantissa = 0; //0%
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenInterfacesModifiedCash.sol#L115

```solidity
113: uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenInterfacesModified.sol#L113





#### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
681: require(_getKYCStatus(borrower), "Borrower not KYC'd");
774: require(_getKYCStatus(borrower), "Borrower not KYC'd");
998: require(_getKYCStatus(borrower), "Borrower not KYC'd");

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L681

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L774

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L998



```solidity
681: require(_getKYCStatus(borrower), "Borrower not KYC'd");
774: require(_getKYCStatus(borrower), "Borrower not KYC'd");

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L681

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L774








### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Using `delete` statement can save gas

#### <ins>Proof Of Concept</ins>

```solidity
259: mintRequestsPerEpoch[epochToClaim][user] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L259

```solidity
580: currentRedeemAmount = 0;
581: currentMintAmount = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L580-L581

```solidity
754: redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L754

```solidity
790: redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L790

```solidity
113: uint startingAllowance = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L113

```solidity
113: uint startingAllowance = 0;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L113




### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

```solidity
74: _notEntered = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L74

```solidity
1438: _notEntered = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1438

```solidity
358: _totalSupply = totalSupply;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L358

```solidity
1436: _notEntered = false;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1436

```solidity
1438: _notEntered = true; // get a gas-refund post-Istanbul
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1438

```solidity
74: _notEntered = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L74

```solidity
1441: _notEntered = true;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1441

```solidity
358: _totalSupply = totalSupply;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L358

```solidity
1439: _notEntered = false;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1439

```solidity
1441: _notEntered = true; // get a gas-refund post-Istanbul
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1441





### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
39: function _setKYCRegistry

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClient.sol#L39

```solidity
54: function _setKYCRequirementGroup

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClient.sol#L54

```solidity
65: function _getKYCStatus

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClient.sol#L65

```solidity
44: function __KYCRegistryClientInitializable_init
58: function __KYCRegistryClientInitializable_init

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientInitializable.sol#L44

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientInitializable.sol#L58



```solidity
58: function __KYCRegistryClientInitializable_init_unchained

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientInitializable.sol#L58

```solidity
29: function _beforeTokenTransfer

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\Cash.sol#L29

```solidity
56: function _beforeTokenTransfer

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSender.sol#L56

```solidity
56: function _beforeTokenTransfer

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSenderReceiver.sol#L56

```solidity
119: function _setFTokenToCToken

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracle.sol#L119

```solidity
210: function _setFTokenToCToken

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L210

```solidity
251: function _setFTokenToChainlinkOracle

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L251

```solidity
324: function _min

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L324

```solidity
179: function getCashPrior

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L179

```solidity
193: function doTransferIn

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L193

```solidity
236: function doTransferOut

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L236

```solidity
479: function mintInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L479

```solidity
491: function mintFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L491

```solidity
552: function redeemInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L552

```solidity
563: function redeemUnderlyingInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L563

```solidity
669: function borrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L669

```solidity
679: function borrowFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L679

```solidity
740: function repayBorrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L740

```solidity
751: function repayBorrowBehalfInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L751

```solidity
845: function liquidateBorrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L845

```solidity
870: function liquidateBorrowFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L870

```solidity
1151: function _setReserveFactorFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1151

```solidity
1182: function _addReservesInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1182

```solidity
1198: function _addReservesFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1198

```solidity
1253: function _reduceReservesFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1253

```solidity
179: function getCashPrior

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L179

```solidity
193: function doTransferIn

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L193

```solidity
236: function doTransferOut

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L236

```solidity
479: function mintInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L479

```solidity
491: function mintFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L491

```solidity
552: function redeemInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L552

```solidity
563: function redeemUnderlyingInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L563

```solidity
669: function borrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L669

```solidity
679: function borrowFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L679

```solidity
740: function repayBorrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L740

```solidity
751: function repayBorrowBehalfInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L751

```solidity
845: function liquidateBorrowInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L845

```solidity
870: function liquidateBorrowFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L870

```solidity
1154: function _setReserveFactorFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1154

```solidity
1185: function _addReservesInternal

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1185

```solidity
1201: function _addReservesFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1201

```solidity
1256: function _reduceReservesFresh

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1256






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

#### <ins>Proof Of Concept</ins>


```solidity
45: mapping(address => uint256) public fTokenToUnderlyingPrice;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracle.sol#L45

```solidity
49: mapping(address => address) public fTokenToCToken;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracle.sol#L49

Can be combined to:

```solidity
struct fTokenStructExample{
  uint256 fTokenToUnderlyingPrice;
  address fTokenToCToken;
}
```

```solidity
55: mapping(address => OracleType) public fTokenToOracleType;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L55

```solidity
58: mapping(address => uint256) public fTokenToUnderlyingPrice;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L58

```solidity
62: mapping(address => address) public fTokenToCToken;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L62

```solidity
70: mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L70

```solidity
73: mapping(address => uint256) public fTokenToUnderlyingPriceCap;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L73

Can be combined to:

```solidity
struct fTokenStructExample{
  OracleType fTokenToOracleType;
  uint256 fTokenToUnderlyingPrice;
  address fTokenToCToken;
  ChainlinkOracleInfo fTokenToChainlinkOracle;
  uint256 fTokenToUnderlyingPriceCap;
}
```


```solidity
273: mapping(address => uint256) internal accountTokens;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L273

```solidity
293: mapping(address => BorrowSnapshot) internal accountBorrows;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L293

Can be combined to:

```solidity
struct structExample{
  uint256 accountTokens;
  BorrowSnapshot accountBorrows;
}
```

```solidity
94: mapping(address => uint) internal accountTokens;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenInterfacesModifiedCash.sol#L94

```solidity
110: mapping(address => BorrowSnapshot) internal accountBorrows;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenInterfacesModifiedCash.sol#L110

Can be combined to:

```solidity
struct structExample{
  uint accountTokens;
  BorrowSnapshot accountBorrows;
}
```


```solidity
92: mapping(address => uint) internal accountTokens;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenInterfacesModified.sol#L92

```solidity
108: mapping(address => BorrowSnapshot) internal accountBorrows;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenInterfacesModified.sol#L108

Can be combined to:

```solidity
struct structExample{
  uint accountTokens;
  BorrowSnapshot accountBorrows;
}
```




### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
221: mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L221

```solidity
582: currentEpoch += epochDifference;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L582

```solidity
630: currentMintAmount += collateralAmountIn;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L630

```solidity
649: currentRedeemAmount += amount;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L649

```solidity
680: ] += amountCashToRedeem;
681: redemptionInfoPerEpoch[currentEpoch].totalBurned += amountCashToRedeem;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L680-L681

```solidity
792: totalCashAmountRefunded += cashAmountBurned;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L792

```solidity
865: redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
867: redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L865

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L867








### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>


```solidity
function transitionEpoch() public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L576

```solidity
function initialize(
    string memory name,
    string memory symbol,
    address kycRegistry,
    uint256 kycRequirementGroup
  ) public initializer {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSender.sol#L46

```solidity
function initialize(
    string memory name,
    string memory symbol,
    address kycRegistry,
    uint256 kycRequirementGroup
  ) public initializer {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSenderReceiver.sol#L46

```solidity
function utilizationRate(
    uint cash,
    uint borrows,
    uint reserves
  ) public pure returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L106

```solidity
function getBorrowRate(
    uint cash,
    uint borrows,
    uint reserves
  ) public view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L126

```solidity
function getSupplyRate(
    uint cash,
    uint borrows,
    uint reserves,
    uint reserveFactorMantissa
  ) public view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L152

```solidity
function getChainlinkOraclePrice(
    address fToken
  ) public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L277

```solidity
function _setImplementation(
    address implementation_,
    bool allowResign,
    bytes memory becomeImplementationData
  ) public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L624

```solidity
function borrowBalanceStored(address account) public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L530

```solidity
function exchangeRateCurrent() public returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L532

```solidity
function exchangeRateStored() public view returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L534

```solidity
function accrueInterest() public returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L538

```solidity
function _setComptroller(
    ComptrollerInterface newComptroller
  ) public returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L554

```solidity
function _setInterestRateModel(
    InterestRateModel newInterestRateModel
  ) public returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L564

```solidity
function delegateToImplementation(
    bytes memory data
  ) public returns (bytes memory) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L1219

```solidity
function delegateToViewImplementation(
    bytes memory data
  ) public view returns (bytes memory) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L1232

```solidity
function initialize(
    address underlying_,
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address kycRegistry_,
    uint kycRequirementGroup_
  ) public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L30

```solidity
function _becomeImplementation(bytes memory data) public virtual override {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCashDelegate.sol#L21

```solidity
function _resignImplementation() public virtual override {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCashDelegate.sol#L39

```solidity
function initialize(
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address kycRegistry_,
    uint256 kycRequirementGroup_
  ) public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L34

```solidity
function borrowBalanceStored(
    address account
  ) public view override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L303

```solidity
function exchangeRateCurrent() public override nonReentrant returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L338

```solidity
function exchangeRateStored() public view override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L348

```solidity
function accrueInterest() public virtual override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L394

```solidity
function _setComptroller(
    ComptrollerInterface newComptroller
  ) public override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1112

```solidity
function _setInterestRateModel(
    InterestRateModel newInterestRateModel
  ) public override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1300

```solidity
function initialize(
    address underlying_,
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address kycRegistry_,
    uint kycRequirementGroup_
  ) public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L30

```solidity
function _becomeImplementation(bytes memory data) public virtual override {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenDelegate.sol#L21

```solidity
function _resignImplementation() public virtual override {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenDelegate.sol#L39

```solidity
function initialize(
    ComptrollerInterface comptroller_,
    InterestRateModel interestRateModel_,
    uint initialExchangeRateMantissa_,
    string memory name_,
    string memory symbol_,
    uint8 decimals_,
    address kycRegistry_,
    uint256 kycRequirementGroup_
  ) public {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L34

```solidity
function borrowBalanceStored(
    address account
  ) public view override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L303

```solidity
function exchangeRateCurrent() public override nonReentrant returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L338

```solidity
function exchangeRateStored() public view override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L348

```solidity
function accrueInterest() public virtual override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L394

```solidity
function _setComptroller(
    ComptrollerInterface newComptroller
  ) public override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1115

```solidity
function _setInterestRateModel(
    InterestRateModel newInterestRateModel
  ) public override returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1303






### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
97: assert(cashProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashFactory.sol#L97

```solidity
106: assert(cashKYCSenderProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderFactory.sol#L106

```solidity
106: assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderReceiverFactory.sol#L106







### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
202: uint8 public decimals;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L202

```solidity
1340: interestRateModel = newInterestRateModel;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1340

```solidity
32: uint8 public decimals;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenInterfacesModifiedCash.sol#L32

```solidity
30: uint8 public decimals;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenInterfacesModified.sol#L30

```solidity
1343: interestRateModel = newInterestRateModel;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1343






### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
750: for (uint256 i = 0; i < size; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L750

```solidity
786: for (uint256 i = 0; i < size; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L786

```solidity
933: for (uint256 i = 0; i < size; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L933

```solidity
961: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L961

```solidity
127: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashFactory.sol#L127

```solidity
137: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderFactory.sol#L137

```solidity
137: for (uint256 i = 0; i < exCallData.length; ++i) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderReceiverFactory.sol#L137

```solidity
163: for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistry.sol#L163

```solidity
180: for (uint256 i = 0; i < length; i++) {
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistry.sol#L180





### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
224: return balanceAfter - balanceBefore;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L224

```solidity
121: uint allowanceNew = startingAllowance - tokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L121

```solidity
422: uint blockDelta = currentBlockNumber - accrualBlockNumberPrior;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L422

```solidity
641: totalSupply = totalSupply - redeemTokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L641

```solidity
818: uint accountBorrowsNew = accountBorrowsPrev - actualRepayAmount;
819: uint totalBorrowsNew = totalBorrows - actualRepayAmount;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L818-L819

```solidity
1026: uint liquidatorSeizeTokens = seizeTokens - protocolSeizeTokens;
1040: totalSupply = totalSupply - protocolSeizeTokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1026

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L1040



```solidity
224: return balanceAfter - balanceBefore;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L224

```solidity
121: uint allowanceNew = startingAllowance - tokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L121

```solidity
422: uint blockDelta = currentBlockNumber - accrualBlockNumberPrior;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L422

```solidity
641: totalSupply = totalSupply - redeemTokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L641

```solidity
818: uint accountBorrowsNew = accountBorrowsPrev - actualRepayAmount;
819: uint totalBorrowsNew = totalBorrows - actualRepayAmount;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L818-L819

```solidity
1029: uint liquidatorSeizeTokens = seizeTokens - protocolSeizeTokens;
1043: totalSupply = totalSupply - protocolSeizeTokens;

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1029

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L1043











### <a href="#Summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Use solidity version 0.8.17 to gain some gas boost

Upgrade to the latest solidity version 0.8.17 to get additional gas savings.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\CashManager.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\Proxy.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\factory\CashKYCSenderReceiverFactory.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\interfaces\ICashManager.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\interfaces\IKYCRegistry.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\interfaces\IKYCRegistryClient.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\interfaces\IMulticall.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistry.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClient.sol#L20

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientConstructable.sol#L19

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\kyc\KYCRegistryClientInitializable.sol#L20

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\Cash.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSender.sol#L16

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/cash\token\CashKYCSenderReceiver.sol#L16

```solidity
pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\IOndoPriceOracle.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\IOndoPriceOracleV2.sol#L15

```solidity
pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\JumpRateModelV2.sol#L1

```solidity
pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracle.sol#L15

```solidity
pragma solidity 0.8.16;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L15

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\test\upgrades\IMulticall.sol#L16

```solidity
pragma solidity ^0.5.12;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cErc20ModifiedDelegator.sol#L7

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CCashDelegate.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cCash\CTokenInterfacesModifiedCash.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CErc20.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenDelegate.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenInterfacesModified.sol#L2

```solidity
pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\tokens\cToken\CTokenModified.sol#L2





### <a href="#Summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
284: ChainlinkOracleInfo memory chainlinkInfo = fTokenToChainlinkOracle[fToken];

```

https://github.com/code-423n4/2023-01-ondo/tree/main/contracts/lending\OndoPriceOracleV2.sol#L284



