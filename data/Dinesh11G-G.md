### 1st BUG
Don't Initialize Variables with Default Value

#### Impact
Issue Information: 
Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Example
🤦 Bad:

uint256 x = 0;
bool y = false;
🚀 Good:

uint256 x;
bool y;

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::45 => uint256 length = 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::46 => uint256 length = 0;
2023-01-ondo/contracts/lending/CompoundLens.sol::85 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::135 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::167 => for (uint i = 0; i < cTokenCount; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::168 => for (uint i = 0; i < len; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::258 => for (uint i = 0; i < len; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::873 => for (uint i = 0; i < assets.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1183 => for (uint i = 0; i < allMarkets.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1237 => for (uint i = 0; i < numMarkets; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1363 => for (uint i = 0; i < affectedUsers.length; ++i) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1656 => for (uint i = 0; i < cTokens.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1662 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1668 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1673 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1735 => for (uint i = 0; i < numTokens; ++i) {
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::258 => uint32 lower = 0;
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::201 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::241 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::284 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/tokens/cToken.sol::306 => uint startingAllowance = 0;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::107 => for (uint i = 0; i < configs.length; i++) {
```
#### Tools used
Manual VS Code review

### 2nd BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
2023-01-ondo/contracts/cash/CashManager.sol::749 => uint256 size = redeemers.length;
2023-01-ondo/contracts/cash/CashManager.sol::785 => uint256 size = refundees.length;
2023-01-ondo/contracts/cash/CashManager.sol::932 => uint256 size = accounts.length;
2023-01-ondo/contracts/cash/CashManager.sol::960 => results = new bytes[](exCallData.length);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/AccessControlEnumerable.sol::69 => return _roleMembers[role].length();
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::76 => if (data.length > 0 || forceCall) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::196 => if (data.length > 0 || forceCall) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol::113 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Address.sol::41 => return account.code.length > 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Address.sol::238 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::59 => set._indexes[value] = set._values.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::83 => uint256 lastIndex = set._values.length - 1;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::120 => function _length(Set storage set) private view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::121 => return set._values.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::193 => function length(Bytes32Set storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::194 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::274 => function length(AddressSet storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::275 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::359 => function length(UintSet storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/EnumerableSet.sol::360 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::45 => uint256 length = 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::47 => length++;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::50 => return toHexString(value, length);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::56 => function toHexString(uint256 value, uint256 length)
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::61 => bytes memory buffer = new bytes(2 * length + 2);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::64 => for (uint256 i = 2 * length + 1; i > 1; --i) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Strings.sol::68 => require(value == 0, "Strings: hex length insufficient");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol::29 => revert("ECDSA: invalid signature length");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol::56 => if (signature.length == 65) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol::198 => return keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n", Strings.toString(s.length), s));
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/access/AccessControlEnumerableUpgradeable.sol::78 => return _roleMembers[role].length();
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::72 => return _allTokens.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::132 => uint256 length = ERC721Upgradeable.balanceOf(to);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::133 => _ownedTokens[to][length] = tokenId;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::134 => _ownedTokensIndex[tokenId] = length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::142 => _allTokensIndex[tokenId] = _allTokens.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721EnumerableUpgradeable.sol::185 => uint256 lastTokenIndex = _allTokens.length - 1;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::139 => bytes(baseURI).length > 0
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::490 => if (reason.length == 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol::41 => return account.code.length > 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol::203 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::67 => set._indexes[value] = set._values.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::91 => uint256 lastIndex = set._values.length - 1;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::128 => function _length(Set storage set) private view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::129 => return set._values.length;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::201 => function length(Bytes32Set storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::202 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::282 => function length(AddressSet storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::283 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::368 => function length(UintSet storage set) internal view returns (uint256) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/EnumerableSetUpgradeable.sol::369 => return _length(set._inner);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::46 => uint256 length = 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::48 => length++;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::51 => return toHexString(value, length);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::57 => function toHexString(uint256 value, uint256 length)
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::62 => bytes memory buffer = new bytes(2 * length + 2);
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::65 => for (uint256 i = 2 * length + 1; i > 1; --i) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/StringsUpgradeable.sol::69 => require(value == 0, "Strings: hex length insufficient");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::126 => results = new bytes[](exCallData.length);
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::136 => results = new bytes[](exCallData.length);
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::136 => results = new bytes[](exCallData.length);
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::162 => uint256 length = addresses.length;
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::163 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::179 => uint256 length = addresses.length;
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::180 => for (uint256 i = 0; i < length; i++) {
2023-01-ondo/contracts/lending/CompoundLens.sol::83 => uint cTokenCount = cTokens.length;
2023-01-ondo/contracts/lending/CompoundLens.sol::133 => uint cTokenCount = cTokens.length;
2023-01-ondo/contracts/lending/CompoundLens.sol::163 => uint cTokenCount = cTokens.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::165 => uint len = cTokens.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::256 => uint len = userAssetList.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::270 => storedList[assetIndex] = storedList[storedList.length - 1];
2023-01-ondo/contracts/lending/compound/Comptroller.sol::271 => storedList.length--;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::873 => for (uint i = 0; i < assets.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1183 => for (uint i = 0; i < allMarkets.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1232 => uint numMarkets = cTokens.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1233 => uint numBorrowCaps = newBorrowCaps.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1354 => require(affectedUsers.length == amounts.length, "Invalid input");
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1363 => for (uint i = 0; i < affectedUsers.length; ++i) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1656 => for (uint i = 0; i < cTokens.length; i++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1662 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1668 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1673 => for (uint j = 0; j < holders.length; j++) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1729 => uint numTokens = cTokens.length;
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1731 => numTokens == supplySpeeds.length && numTokens == borrowSpeeds.length,
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::125 => targets.length == values.length &&
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::126 => targets.length == signatures.length &&
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::127 => targets.length == calldatas.length,
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::131 => targets.length != 0,
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::135 => targets.length <= proposalMaxOperations,
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::201 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::241 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::284 => for (uint i = 0; i < proposal.targets.length; i++) {
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::353 => if (bytes(signature).length == 0) {
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::107 => for (uint i = 0; i < configs.length; i++) {
2023-01-ondo/contracts/lending/compound/uniswap/UniswapConfig.sol::305 => require(configs.length <= maxTokens, "too many configs");
2023-01-ondo/contracts/lending/compound/uniswap/UniswapConfig.sol::306 => numTokens = configs.length;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapConfig.sol::573 => if (i < configs.length) return configs[i];
```
#### Tools used
Manual VS Code review

### 3rd BUG
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::76 => if (data.length > 0 || forceCall) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::196 => if (data.length > 0 || forceCall) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/SafeERC20.sol::113 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Address.sol::41 => return account.code.length > 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Address.sol::238 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/Counters.sol::34 => require(value > 0, "Counter: decrement overflow");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/utils/cryptography/ECDSA.sol::147 => if (uint256(s) > 0x7FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF5D576E7357A4501DDFE92F46681B20A0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::139 => bytes(baseURI).length > 0
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol::41 => return account.code.length > 0;
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol::203 => if (returndata.length > 0) {
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/utils/CounterUpgradeable.sol::34 => require(value > 0, "Counter: decrement overflow");
2023-01-ondo/contracts/lending/compound/Comptroller.sol::383 => if (shortfall > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::408 => if (redeemTokens == 0 && redeemAmount > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::467 => if (shortfall > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1386 => if (amountToSubtract > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1458 => if (deltaBlocks > 0 && supplySpeed > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1461 => Double memory ratio = supplyTokens > 0
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1469 => } else if (deltaBlocks > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1490 => if (deltaBlocks > 0 && borrowSpeed > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1496 => Double memory ratio = borrowAmount > 0
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1504 => } else if (deltaBlocks > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1615 => if (deltaBlocks > 0 && compSpeed > 0) {
2023-01-ondo/contracts/lending/compound/Comptroller.sol::1694 => if (amount > 0 && amount <= compRemaining) {
2023-01-ondo/contracts/lending/compound/ExponentialNoError.sol::238 => require(b > 0, errorMessage);
2023-01-ondo/contracts/lending/compound/SafeMath.sol::165 => require(b > 0, errorMessage);
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::224 => return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::314 => if (srcRep != dstRep && amount > 0) {
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::317 => uint96 srcRepOld = srcRepNum > 0
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::330 => uint96 dstRepOld = dstRepNum > 0
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::355 => nCheckpoints > 0 &&
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::136 => require(b > 0, errorMessage);
2023-01-ondo/contracts/lending/compound/tokens/cToken.sol::253 => initialExchangeRateMantissa > 0,
2023-01-ondo/contracts/lending/compound/tokens/cToken.sol::1078 => if (redeemTokensIn > 0) {
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::109 => require(config.baseUnit > 0, "baseUnit must be greater than zero");
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::162 => require(usdPerEth > 0, "ETH price not set, cannot convert to dollars");
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::273 => if (reporterPrice > 0) {
```
#### Tools used
Manual VS Code review

### 4th BUG
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::50 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721PresetMinterPauserAutoIdUpgradeable.sol::51 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::44 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2023-01-ondo/contracts/cash/factory/CashKYCSenderReceiverFactory.sol::45 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::32 => keccak256(
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::36 => bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");
2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol::93 => bytes32 structHash = keccak256(
2023-01-ondo/contracts/cash/token/Cash.sol::22 => bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");
2023-01-ondo/contracts/cash/token/CashKYCSender.sol::27 => keccak256("KYC_CONFIGURER_ROLE");
2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol::27 => keccak256("KYC_CONFIGURER_ROLE");
2023-01-ondo/contracts/lending/CompoundLens.sol::198 => return (keccak256(abi.encodePacked((a))) ==
2023-01-ondo/contracts/lending/CompoundLens.sol::199 => keccak256(abi.encodePacked((b))));
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::40 => keccak256(
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::46 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::196 => bytes32 domainSeparator = keccak256(
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::199 => keccak256(bytes(name)),
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::204 => bytes32 structHash = keccak256(
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::207 => bytes32 digest = keccak256(
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::39 => keccak256(
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::45 => keccak256("Ballot(uint256 proposalId,uint8 support)");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::223 => keccak256(abi.encode(target, value, signature, data, eta))
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::412 => bytes32 domainSeparator = keccak256(
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::415 => keccak256(bytes(name)),
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::420 => bytes32 structHash = keccak256(
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::423 => bytes32 digest = keccak256(
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::298 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::317 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::335 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::356 => callData = abi.encodePacked(bytes4(keccak256(bytes(signature))), data);
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::81 => bytes32 constant ethHash = keccak256(abi.encodePacked("ETH"));
2023-01-ondo/contracts/lending/compound/uniswap/UniswapConfig.sol::1079 => return getTokenConfigBySymbolHash(keccak256(abi.encodePacked(symbol)));
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::24 => keccak256("TIMELOCK_UPDATE_ROLE");
```
#### Tools used
Manual VS Code review

### 5th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/access/Ownable.sol::66 => require(newOwner != address(0), "Ownable: new owner is the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::105 => revert("ERC1967Upgrade: new implementation is not UUPS");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/proxy/ERC1967Upgrade.sol::135 => require(newAdmin != address(0), "ERC1967: new admin is the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::264 => require(from != address(0), "ERC20: transfer from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::265 => require(to != address(0), "ERC20: transfer to the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::270 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::314 => require(account != address(0), "ERC20: burn from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::319 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::348 => require(owner != address(0), "ERC20: approve from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts/token/ERC20.sol::349 => require(spender != address(0), "ERC20: approve to the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PausableUpgradeable.sol::42 => require(!paused(), "ERC20Pausable: token transfer while paused");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::280 => require(from != address(0), "ERC20: transfer from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::281 => require(to != address(0), "ERC20: transfer to the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::286 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::330 => require(account != address(0), "ERC20: burn from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::335 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::364 => require(owner != address(0), "ERC20: approve from the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol::365 => require(spender != address(0), "ERC20: approve to the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::92 => require(owner != address(0), "ERC721: address zero is not a valid owner");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::158 => require(to != owner, "ERC721: approval to current owner");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::415 => require(to != address(0), "ERC721: transfer to the zero address");
2023-01-ondo/contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol::491 => revert("ERC721: transfer to non ERC721Receiver implementer");AddressUpgradeable.sol::168 => functionStaticCall(target, data, "Address: low-level static call failed");
2023-01-ondo/contracts/lending/compound/SafeMath.sol::102 => require(c / a == b, "SafeMath: multiplication overflow");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::46 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::110 => amount = safe96(rawAmount, "Comp::approve: amount exceeds 96 bits");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::135 => uint96 amount = safe96(rawAmount, "Comp::transfer: amount exceeds 96 bits");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::154 => uint96 amount = safe96(rawAmount, "Comp::approve: amount exceeds 96 bits");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::211 => require(signatory != address(0), "Comp::delegateBySig: invalid signature");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::212 => require(nonce == nonces[signatory]++, "Comp::delegateBySig: invalid nonce");
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::213 => require(now <= expiry, "Comp::delegateBySig: signature expired");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::45 => keccak256("Ballot(uint256 proposalId,uint8 support)");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::456 => require(support <= 2, "GovernorBravo::castVoteInternal: invalid vote type");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::461 => "GovernorBravo::castVoteInternal: voter already voted"
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::494 => require(msg.sender == admin, "GovernorBravo::_setVotingDelay: admin only");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::497 => "GovernorBravo::_setVotingDelay: invalid voting delay"
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::510 => require(msg.sender == admin, "GovernorBravo::_setVotingPeriod: admin only");
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::582 => require(msg.sender == admin, "GovernorBravo::_initiate: admin only");
2023-01-ondo/contracts/lending/compound/governance/Timelock.sol::97 => require(c / a == b, "SafeMath: multiplication overflow");
```
#### Tools used
Manual VS Code review

### 6th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2023-01-ondo/contracts/lending/OndoPriceOracleV2.sol::77 => uint256 public maxChainlinkOracleTimeDelay = 90000; // 25 hours
2023-01-ondo/contracts/lending/compound/ExponentialNoError.sol::13 => uint constant halfExpScale = expScale / 2;
2023-01-ondo/contracts/lending/compound/ExponentialNoError.sol::97 => require(n < 2 ** 224, errorMessage);
2023-01-ondo/contracts/lending/compound/governance/Comp.sol::261 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
2023-01-ondo/contracts/lending/compound/governance/GovernorBravoDelegate.sol::32 => uint public constant quorumVotes = 400000e18; // 400,000 = 4% of Comp
2023-01-ondo/contracts/lending/compound/tokens/Exponential.sol::14 => uint constant halfExpScale = expScale / 2;
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::203 => require(anchorPrice < 2 ** 248, "Anchor price too large");
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::207 => require(reportedPrice < 2 ** 248, "Reported price too large");
2023-01-ondo/contracts/lending/compound/uniswap/UniswapAnchoredView.sol::227 => require(anchorPrice < 2 ** 248, "Anchor price too large");
2023-01-ondo/contracts/lending/ondo/ondo-token/LinearTimelock.sol::104 => (((investorParam.initialBalance * elapsed) / vestingPeriod) * 2) / 3,
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol::111 => uint public constant protocolSeizeShareMantissa = 2.8e16; //2.8%
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::14 => uint constant halfExpScale = expScale / 2;
2023-01-ondo/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol::98 => require(n < 2 ** 224, errorMessage);
```
#### Tools used
Manual VS Code review