## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```solidity
1437:  modifier nonReentrant() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol

```solidity
// events coming after functions
74:  event Transfer(address indexed from, address indexed to, uint256 amount);
75:  event Approval(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol

```solidity
// events coming after functions
76:  event Transfer(address indexed from, address indexed to, uint256 amount);
77:  event Approval(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```solidity
// modifier coming after functions
1434:  modifier nonReentrant() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Ownable.sol

```solidity
// modifier coming after functions
54:  modifier onlyOwner() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Comptroller.sol

```solidity
// state variables should come before events
114:  uint224 public constant compInitialIndex = 1e36;
117:  uint internal constant closeFactorMinMantissa = 0.05e18; // 0.05
120:  uint internal constant closeFactorMaxMantissa = 0.9e18; // 0.9
123:  uint internal constant collateralFactorMaxMantissa = 0.98e18; // 0.98
778:  struct AccountLiquidityLocalVars {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapAnchoredView.sol

```solidity
// state variables should come before events
81:  bytes32 constant ethHash = keccak256(abi.encodePacked("ETH"));
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/EIP20Interface.sol

```solidity
// events are coming after functions
76:  event Transfer(address indexed from, address indexed to, uint256 amount);
77:  event Approval(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol

```solidity
// events are coming after functions
41:  event FTokenToCTokenSet(
54:  event UnderlyingPriceSet(
66:  event CTokenOracleSet(address oldOracle, address newOracle);
101:  event PriceCapSet(
114:  event ChainlinkOracleSet(
126:  event MaxChainlinkOracleTimeDelaySet(
137:  event FTokenToOracleTypeSet(address indexed fToken, OracleType oracleType);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/CompoundLens.sol

```solidity
// state variables should not come between functions
91:  struct CTokenBalances {
141:  struct CTokenUnderlyingPrice {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```solidity
//  these events are coming after functions
195:  event RoleAssignedToKYCGroup(
207:  event KYCAddressesAdded(
223:  event KYCAddressAddViaSignature(
238:  event KYCAddressesRemoved(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol

```solidity
// this errors declarations should not come after functions
40:  error RegistryZeroAddress();

//  these events are coming after functions
48:  event KYCRegistrySet(address oldRegistry, address newRegistry);
56:  event KYCRequirementGroupSet(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/ICashManager.sol

```solidity
//  these events are coming after functions
76:   event FeeRecipientSet(address oldFeeRecipient, address newFeeRecipient);
84:   event AssetRecipientSet(address oldAssetRecipient, address newAssetRecipient);
92:   event AssetSenderSet(address oldAssetSender, address newAssetSender);
102:  event MinimumDepositAmountSet(uint256 oldMinimum, uint256 newMinimum);
111:  event MinimumRedeemAmountSet(uint256 oldRedeemMin, uint256 newRedeemMin);
121:  event MintFeeSet(uint256 oldFee, uint256 newFee);
133:  event MintExchangeRateSet(
151:  event MintExchangeRateOverridden(
164:  event ExchangeRateDeltaLimitSet(uint256 oldLimit, uint256 newLimit);
175:  event MintExchangeRateCheckFailed(
189:  event MintLimitSet(uint256 oldLimit, uint256 newLimit);
199:  event RedeemLimitSet(uint256 oldLimit, uint256 newLimit);
209:  event EpochDurationSet(uint256 oldDuration, uint256 newDuration);
218:  event RedemptionRequested(
233:  event MintRequested(
250:  event RedemptionCompleted(
268:  event MintCompleted(
284:  event RedemptionFeesCollected(
297:  event RefundIssued(
311:  event PendingMintBalanceSet(
327:  event PendingRedemptionBalanceSet(

// these errors declarations should not come after functions
335:  error CollateralZeroAddress();
336:  error CashZeroAddress();
337:  error AssetRecipientZeroAddress();
338:  error AssetSenderZeroAddress();
339:  error FeeRecipientZeroAddress();
340:  error MinimumDepositAmountTooSmall();
341:  error ZeroExchangeRate();
343:  error KYCCheckFailed();
344:  error MintRequestAmountTooSmall();
345:  error NoCashToClaim();
346:  error ExchangeRateNotSet();
348:  error EpochNotElapsed();
349:  error EpochExchangeRateAlreadySet();
350:  error UnexpectedMintBalance();
352:  error MintFeeTooLarge();
353:  error MintExceedsRateLimit();
354:  error RedeemAmountCannotBeZero();
355:  error RedeemExceedsRateLimit();
356:  error WithdrawRequestAmountTooSmall();
357:  error CollateralRedemptionTooSmall();
358:  error MustServicePastEpoch();
359:  error CannotServiceFutureEpoch();
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```solidity
// events should come before modifiers, constructor and functions
153:  event CashKYCSenderReceiverDeployed(

// modifiers should all come before functions and constructor
162:  modifier onlyGuardian() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```solidity
// events should come before modifiers, constructor and functions
153:  event CashKYCSenderDeployed(

// modifiers should all come before functions and constructor
162:  modifier onlyGuardian() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```solidity
// events should come before modifiers, constructor and functions
143:  event CashDeployed(

// modifiers should all come before functions and constructor
151:  modifier onlyGuardian() {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```solidity
// modifiers should all come before functions and constructor
557:  modifier updateEpoch() {

885:  modifier checkKYC(address account) {
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```solidity
 // All function visiblities are mixed throughout the code, they should follow the right order
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```solidity
 // All function visiblities are mixed throughout the code, they should follow the right order
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Ownable.sol

```solidity
// public function coming after external ones
46:  function owner() public view returns (address) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Comptroller.sol

```solidity
 // All function visiblities are mixed throughout the code, they should follow the right order
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapAnchoredView.sol

```solidity
 // All function visiblities are mixed throughout the code, they should follow the right order
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernanceBravoDelegator.sol

```solidity
// fallback/receive function should come right after the contructor
81:  function() external payable {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```solidity
// event is coming before state variables
14:  event NewInterestParams(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```solidity
// public function coming after external ones
46:  function initialize(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```solidity
// public function coming after external ones
46:  function initialize(
```

---

### natSpec missing [3]

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

```solidity
116:interface ISanctionsList {

117:  function isSanctioned(address addr) external view returns (bool);

178:  abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {

304:  function transfer(address dst, uint amount) external virtual returns (bool);

306:  function transferFrom(

312:  function approve(

317:  function allowance(

322:  function balanceOf(address owner) external view virtual returns (uint);

324:  function balanceOfUnderlying(address owner) external virtual returns (uint);

326:  function getAccountSnapshot(

330:  function borrowRatePerBlock() external view virtual returns (uint);

332:  function supplyRatePerBlock() external view virtual returns (uint);

334:  function totalBorrowsCurrent() external virtual returns (uint);

336:  function borrowBalanceCurrent(

340:  function borrowBalanceStored(

344:  function exchangeRateCurrent() external virtual returns (uint);

346:  function exchangeRateStored() external view virtual returns (uint);

348:  function getCash() external view virtual returns (uint);

350:  function accrueInterest() external virtual returns (uint);

352:  function seize(

360:  function _setPendingAdmin(

364:  function _acceptAdmin() external virtual returns (uint);

366:  function _setComptroller(

370:  function _setReserveFactor(

374:  function _reduceReserves(uint reduceAmount) external virtual returns (uint);

376:  function _setInterestRateModel(

381:  contract CErc20Storage {

388:  abstract contract CErc20Interface is CErc20Storage {

391:  function mint(uint mintAmount) external virtual returns (uint);

393:  function redeem(uint redeemTokens) external virtual returns (uint);

395:  function redeemUnderlying(uint redeemAmount) external virtual returns (uint);

397:  function borrow(uint borrowAmount) external virtual returns (uint);

399:  function repayBorrow(uint repayAmount) external virtual returns (uint);

401:  function repayBorrowBehalf(

406:  function liquidateBorrow(

412:  function sweepToken(EIP20NonStandardInterface token) external virtual;

416:  function _addReserves(uint addAmount) external virtual returns (uint);

419:  contract CDelegationStorage {

426:  abstract contract CDelegatorInterface is CDelegationStorage {

// @param missing
430:  event NewImplementation(address oldImplementation, address newImplementation);

445:  abstract contract CDelegateInterface is CDelegationStorage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```solidity
6:  interface CompLike {
7:  function delegate(address delegatee) external;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol

```solidity
29:  function truncate(Exp memory exp) internal pure returns (uint) {
37:  function mul_ScalarTruncate(
48:  function mul_ScalarTruncateAddUInt(
60:  function lessThanExp(
70:  function lessThanOrEqualExp(
80:  function greaterThanExp(
90:  function isZeroExp(Exp memory value) internal pure returns (bool) {
94:  function safe224(
102:  function safe32(
110:  function add_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
114:  function add_(
121:  function add_(uint a, uint b) internal pure returns (uint) {
125:  function sub_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
129:  function sub_(
136:  function sub_(uint a, uint b) internal pure returns (uint) {
140:  function mul_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
144:  function mul_(Exp memory a, uint b) internal pure returns (Exp memory) {
148:  function mul_(uint a, Exp memory b) internal pure returns (uint) {
152:  function mul_(
159:  function mul_(Double memory a, uint b) internal pure returns (Double memory) {
163:  function mul_(uint a, Double memory b) internal pure returns (uint) {
167:  function mul_(uint a, uint b) internal pure returns (uint) {
171:  function div_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
175:  function div_(Exp memory a, uint b) internal pure returns (Exp memory) {
179:  function div_(uint a, Exp memory b) internal pure returns (uint) {
183:  function div_(
190:  function div_(Double memory a, uint b) internal pure returns (Double memory) {
194:  function div_(uint a, Double memory b) internal pure returns (uint) {
198:  function div_(uint a, uint b) internal pure returns (uint) {
202:  function fraction(uint a, uint b) internal pure returns (Double memory) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol

```solidity
4:  contract ComptrollerErrorReporter {

// @params and @return missing
58:  function fail(Error err, FailureInfo info) internal returns (uint) {

// @params and @return missing
67:  function failOpaque(

78:  contract TokenErrorReporter {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol

```solidity
9:  function name() external view returns (string memory);

11:  function symbol() external view returns (string memory);

13:  function decimals() external view returns (uint8);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol

```solidity
10:  function enterMarkets(

18:  function mintAllowed(

24:  function mintVerify(

31:  function redeemAllowed(

37:  function redeemVerify(

44:  function borrowAllowed(

50:  function borrowVerify(

56:  function repayBorrowAllowed(

63:  function repayBorrowVerify(

71:  function liquidateBorrowAllowed(

79:  function liquidateBorrowVerify(

88:  function seizeAllowed(

96:  function seizeVerify(

104:  function transferAllowed(

111:  function transferVerify(

120:  function liquidateCalculateSeizeTokens(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol

```solidity
240:  function transfer(address dst, uint amount) external virtual returns (bool);

242:  function transferFrom(

248:  function approve(

253:  function allowance(

258:  function balanceOf(address owner) external view virtual returns (uint);

260:  function balanceOfUnderlying(address owner) external virtual returns (uint);

262:  function getAccountSnapshot(

266:  function borrowRatePerBlock() external view virtual returns (uint);

268:  function supplyRatePerBlock() external view virtual returns (uint);

270:  function totalBorrowsCurrent() external virtual returns (uint);

272:  function borrowBalanceCurrent(

276:  function borrowBalanceStored(

280:  function exchangeRateCurrent() external virtual returns (uint);

282:  function exchangeRateStored() external view virtual returns (uint);

284:  function getCash() external view virtual returns (uint);

286:  function accrueInterest() external virtual returns (uint);

288:  function seize(

296:  function _setPendingAdmin(

300:  function _acceptAdmin() external virtual returns (uint);

302:  function _setComptroller(

306:  function _setReserveFactor(

310:  function _reduceReserves(uint reduceAmount) external virtual returns (uint);

312:  function _setInterestRateModel(

317:  contract CErc20Storage {

324:  abstract contract CErc20Interface is CErc20Storage {

327:  function mint(uint mintAmount) external virtual returns (uint);

329:  function redeem(uint redeemTokens) external virtual returns (uint);

331:  function redeemUnderlying(uint redeemAmount) external virtual returns (uint);

333:  function borrow(uint borrowAmount) external virtual returns (uint);

335:  function repayBorrow(uint repayAmount) external virtual returns (uint);

337:  function repayBorrowBehalf(

342:  function liquidateBorrow(

348:  function sweepToken(EIP20NonStandardInterface token) external virtual;

352:  function _addReserves(uint addAmount) external virtual returns (uint);

355:  contract CDelegationStorage {

362:  abstract contract CDelegatorInterface is CDelegationStorage {

381:  abstract contract CDelegateInterface is CDelegationStorage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```solidity
118:  interface ISanctionsList {

119:  function isSanctioned(address addr) external view returns (bool);

180:  abstract contract CTokenInterface is CTokenStorage, OndoKYCStorage {

306:  function transfer(address dst, uint amount) external virtual returns (bool);

308:  function transferFrom(

314:  function approve(

319:  function allowance(

324:  function balanceOf(address owner) external view virtual returns (uint);

326:  function balanceOfUnderlying(address owner) external virtual returns (uint);

328:  function getAccountSnapshot(

332:  function borrowRatePerBlock() external view virtual returns (uint);

334:  function supplyRatePerBlock() external view virtual returns (uint);

336:  function totalBorrowsCurrent() external virtual returns (uint);

338:  function borrowBalanceCurrent(

342:  function borrowBalanceStored(

346:  function exchangeRateCurrent() external virtual returns (uint);

348:  function exchangeRateStored() external view virtual returns (uint);

350:  function getCash() external view virtual returns (uint);

352:  function accrueInterest() external virtual returns (uint);

354:  function seize(

362:  function _setPendingAdmin(

366:  function _acceptAdmin() external virtual returns (uint);

368:  function _setComptroller(

372:  function _setReserveFactor(

376:  function _reduceReserves(uint reduceAmount) external virtual returns (uint);

378:  function _setInterestRateModel(

383:  contract CErc20Storage {

390:abstract contract CErc20Interface is CErc20Storage {

393:  function mint(uint mintAmount) external virtual returns (uint);

395:  function redeem(uint redeemTokens) external virtual returns (uint);

397:  function redeemUnderlying(uint redeemAmount) external virtual returns (uint);

399:  function borrow(uint borrowAmount) external virtual returns (uint);

401:  function repayBorrow(uint repayAmount) external virtual returns (uint);

403:  function repayBorrowBehalf(

408:  function liquidateBorrow(

414:  function sweepToken(EIP20NonStandardInterface token) external virtual;

418:  function _addReserves(uint addAmount) external virtual returns (uint);

421:  contract CDelegationStorage {

428:  abstract contract CDelegatorInterface is CDelegationStorage {

447:  abstract contract CDelegateInterface is CDelegationStorage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```solidity
9:  interface ComptrollerInterface {

19:  function enterMarkets(

23:  function exitMarket(address cToken) external returns (uint256);

27:  function mintAllowed(

33:  function mintVerify(

40:  function redeemAllowed(

46:  function redeemVerify(

53:  function borrowAllowed(

59:  function borrowVerify(

65:  function repayBorrowAllowed(

72:  function repayBorrowVerify(

80:  function liquidateBorrowAllowed(

88:  function liquidateBorrowVerify(

97:  function seizeAllowed(

105:  function seizeVerify(

113:  function transferAllowed(

120:  function transferVerify(

129:  function liquidateCalculateSeizeTokens(

183:  contract CTokenStorage {

298:  interface ISanctionsList {

364:  contract CTokenInterface is CTokenStorage, OndoKYCStorage {

499:  function transfer(address dst, uint256 amount) external returns (bool);

501:  function transferFrom(

507:  function approve(address spender, uint256 amount) external returns (bool);

509:  function allowance(

514:  function balanceOf(address owner) external view returns (uint256);

516:  function balanceOfUnderlying(address owner) external returns (uint256);

518:  function getAccountSnapshot(

522:  function borrowRatePerBlock() external view returns (uint256);

524:  function supplyRatePerBlock() external view returns (uint256);

526:  function totalBorrowsCurrent() external returns (uint256);

528:  function borrowBalanceCurrent(address account) external returns (uint256);

530:  function borrowBalanceStored(address account) public view returns (uint256);

532:  function exchangeRateCurrent() public returns (uint256);

534:  function exchangeRateStored() public view returns (uint256);

536:  function getCash() external view returns (uint256);

538:  function accrueInterest() public returns (uint256);

540:  function seize(

548:  function _setPendingAdmin(

552:  function _acceptAdmin() external returns (uint256);

554:  function _setComptroller(

558:  function _setReserveFactor(

562:  function _reduceReserves(uint256 reduceAmount) external returns (uint256);

564:  function _setInterestRateModel(

569:  contract CErc20Storage {

576:  contract CErc20Interface is CErc20Storage {

579:  function mint(uint256 mintAmount) external returns (uint256);

581:  function redeem(uint256 redeemTokens) external returns (uint256);

583:  function redeemUnderlying(uint256 redeemAmount) external returns (uint256);

585:  function borrow(uint256 borrowAmount) external returns (uint256);

587:  function repayBorrow(uint256 repayAmount) external returns (uint256);

589:  function repayBorrowBehalf(

594:  function liquidateBorrow(

602:  function _addReserves(uint256 addAmount) external returns (uint256);

605:  contract CDelegationStorage {

612:  contract CDelegatorInterface is CDelegationStorage {

631:  contract CDelegateInterface is CDelegationStorage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/LinearTimelock.sol

```solidity
6:   abstract contract LinearTimelock {

26:  constructor(

38:  function passedCliff() public view returns (bool) {

// @return missing
43:  function passedAllVestingPeriods() public view returns (bool) {

// @param and @return missing
50:  function getVestedBalance(

75:  function _getTrancheInfo(

95:  function _proportionAvailable(

122:  function safe32(

130:  function safe96(

138:  function add96(

148:  function sub96(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/IOndo.sol

```solidity
4:   interface IOndo {

// @param missing
14:  function updateTrancheBalance(

// @param and @return missing
23:  function getFreedBalance(address account) external view returns (uint96);

// @param and @return missing
26:  function getVestedBalance(

30:  function delegate(address) external;

32:  function transfer(address, uint256) external;

34:  function getPriorVotes(

39:  function grantRole(bytes32 role, address account) external;

41:  function balanceOf(address account) external view returns (uint256);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Unitroller.sol

```solidity
35:  constructor(address _admin) public {

40:  function _setPendingImplementation(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/PriceOracle.sol

```solidity
5: contract PriceOracle {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Ownable.sol

```solidity
14:  constructor() public {

// @param missing
22:  function transferOwnership(address to) external onlyOwner {
33:  function acceptOwnership() external {

// @return missing
46:  function owner() public view returns (address) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ExponentialNoError.sol

```solidity
// @param and @return missing
28:  function truncate(Exp memory exp) internal pure returns (uint) {
36:  function mul_ScalarTruncate(
47:  function mul_ScalarTruncateAddUInt(
59:  function lessThanExp(
69:  function lessThanOrEqualExp(
79:  function greaterThanExp(
89:  function isZeroExp(Exp memory value) internal pure returns (bool) {
93:  function safe224(
101:  function safe32(
109:  function add_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
113:  function add_(
120:  function add_(uint a, uint b) internal pure returns (uint) {
124:  function add_(
134:  function sub_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
138:  function sub_(
145:  function sub_(uint a, uint b) internal pure returns (uint) {
149:  function sub_(
158:  function mul_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
162:  function mul_(Exp memory a, uint b) internal pure returns (Exp memory) {
166:  function mul_(uint a, Exp memory b) internal pure returns (uint) {
170:  function mul_(
177:  function mul_(Double memory a, uint b) internal pure returns (Double memory) {
181:  function mul_(uint a, Double memory b) internal pure returns (uint) {
185:  function mul_(uint a, uint b) internal pure returns (uint) {
189:  function mul_(
202:  function div_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
206:  function div_(Exp memory a, uint b) internal pure returns (Exp memory) {
210:  function div_(uint a, Exp memory b) internal pure returns (uint) {
214:  function div_(
221:  function div_(Double memory a, uint b) internal pure returns (Double memory) {
225:  function div_(uint a, Double memory b) internal pure returns (uint) {
229:  function div_(uint a, uint b) internal pure returns (uint) {
233:  function div_(
242:  function fraction(uint a, uint b) internal pure returns (Double memory) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ErrorReporter.sol

```solidity
3:  contract ComptrollerErrorReporter {

// @param and @return missing
57:  function fail(Error err, FailureInfo info) internal returns (uint) {
66:  function failOpaque(

77:  contract TokenErrorReporter {

// @param and @return missing
194:  function fail(Error err, FailureInfo info) internal returns (uint) {
203:  function failOpaque(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerStorage.sol

```solidity
6:  contract UnitrollerAdminStorage {

28:  contract ComptrollerV1Storage is UnitrollerAdminStorage {

55:  contract ComptrollerV2Storage is ComptrollerV1Storage {

91:  contract ComptrollerV3Storage is ComptrollerV2Storage {

124:  contract ComptrollerV4Storage is ComptrollerV3Storage {

132:  contract ComptrollerV5Storage is ComptrollerV4Storage {

140:  contract ComptrollerV6Storage is ComptrollerV5Storage {

148:  contract ComptrollerV7Storage is ComptrollerV6Storage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerInterface.sol

```solidity
9:  function enterMarkets(

13:  function exitMarket(address cToken) external returns (uint);

17:  function mintAllowed(

23:  function mintVerify(

30:  function redeemAllowed(

36:  function redeemVerify(

43:  function borrowAllowed(

49:  function borrowVerify(

55:  function repayBorrowAllowed(

62:  function repayBorrowVerify(

70:  function liquidateBorrowAllowed(

78:  function liquidateBorrowVerify(

87:  function seizeAllowed(

95:  function seizeVerify(

103:  function transferAllowed(

110:  function transferVerify(

119:  function liquidateCalculateSeizeTokens(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Comptroller.sol

```solidity
125:  constructor(address _admin) public {

359:  function redeemAllowedInternal(

// @param missing
797:  function getAccountLiquidity(

// @param missing
815:  function getAccountLiquidityInternal(

1182:  function _addMarketInternal(address cToken) internal {

1190:  function _initializeMarket(address cToken) internal {

1283:  function _setMintPaused(CToken cToken, bool state) public returns (bool) {

1299:  function _setBorrowPaused(CToken cToken, bool state) public returns (bool) {

1315:  function _setTransferPaused(bool state) public returns (bool) {

1327:  function _setSeizePaused(bool state) public returns (bool) {

1339:  function _become(Unitroller unitroller) public {

1348:  function fixBadAccruals(

// @return missing
1401:  function adminOrInitializing() internal view returns (bool) {

// @param missing
1479:  function updateCompBorrowIndex(

1785:  function getBlockNumber() public view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapLib.sol

```solidity
// @param and @returns missing
17:  function fraction(

// @param missing and @returns missing
26:  function decode112with18(uq112x112 memory self) internal pure returns (uint) {

// @param missing and @returns missing
46:  function currentCumulativePrices(

83:   interface IUniswapV2Pair {

84:  function getReserves()

89:  function price0CumulativeLast() external view returns (uint);

91:  function price1CumulativeLast() external view returns (uint);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapConfig.sol

```solidity
6:   interface CErc20 {

7:  function underlying() external view returns (address);

10:  contract UniswapConfig {

569:  function get(

589:  function getReporterIndex(address reporter) internal view returns (uint) {

619:  function getCTokenIndex(address cToken) internal view returns (uint) {

649:  function getUnderlyingIndex(address underlying) internal view returns (uint) {

679:  function getSymbolHashIndex(bytes32 symbolHash) internal view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapAnchoredView.sol

```solidity
11:  struct Observation {

16:  struct PriceData {

21:  contract UniswapAnchoredView is

153:  function priceInternal(

269:  function isWithinAnchor(

// @param and @return missing
285:  function currentCumulativePrice(

// @return missing
301:  function fetchEthAnchorPrice() internal returns (uint) {

314:  function fetchAnchorPrice(

368:  function pokeWindowValues(

403:  function activateFailover(bytes32 symbolHash) external onlyOwner {

414:  function deactivateFailover(bytes32 symbolHash) external onlyOwner {

421:  function mul(uint a, uint b) internal pure returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cToken.sol


```solidity
// @param and @return missing
2108:  function checkTransferIn(
2118:  function doTransferIn(address from, uint amount) internal returns (Error);
2125:  function doTransferOut(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cErc20Delegator.sol

```solidity
9:   interface ComptrollerInterface {

19:  function enterMarkets(

23:  function exitMarket(address cToken) external returns (uint256);

27:  function mintAllowed(

33:  function mintVerify(

40:  function redeemAllowed(

46:  function redeemVerify(

53:  function borrowAllowed(

59:  function borrowVerify(

65:  function repayBorrowAllowed(

72:  function repayBorrowVerify(

80:  function liquidateBorrowAllowed(

88:  function liquidateBorrowVerify(

97:  function seizeAllowed(

105:  function seizeVerify(

113:  function transferAllowed(

120:  function transferVerify(

129:  function liquidateCalculateSeizeTokens(

// @return missing
148:  function isInterestRateModel() external pure returns (bool);

183:  contract CTokenStorage {

296:  contract CTokenInterface is CTokenStorage {

431:  function transfer(address dst, uint256 amount) external returns (bool);

433:  function transferFrom(

439:  function approve(address spender, uint256 amount) external returns (bool);

441:  function allowance(

446:  function balanceOf(address owner) external view returns (uint256);

448:  function balanceOfUnderlying(address owner) external returns (uint256);

450:  function getAccountSnapshot(

454:  function borrowRatePerBlock() external view returns (uint256);

456:  function supplyRatePerBlock() external view returns (uint256);

458:  function totalBorrowsCurrent() external returns (uint256);

460:  function borrowBalanceCurrent(address account) external returns (uint256);

462:  function borrowBalanceStored(address account) public view returns (uint256);

464:  function exchangeRateCurrent() public returns (uint256);

466:  function exchangeRateStored() public view returns (uint256);

468:  function getCash() external view returns (uint256);

470:  function accrueInterest() public returns (uint256);

472:  function seize(

480:  function _setPendingAdmin(

484:  function _acceptAdmin() external returns (uint256);

486:  function _setComptroller(

490:  function _setReserveFactor(

494:  function _reduceReserves(uint256 reduceAmount) external returns (uint256);

496:  function _setInterestRateModel(

501:  contract CErc20Storage {

508:  contract CErc20Interface is CErc20Storage {

511:  function mint(uint256 mintAmount) external returns (uint256);

513:  function redeem(uint256 redeemTokens) external returns (uint256);

515:  function redeemUnderlying(uint256 redeemAmount) external returns (uint256);

517:  function borrow(uint256 borrowAmount) external returns (uint256);

519:  function repayBorrow(uint256 repayAmount) external returns (uint256);

521:  function repayBorrowBehalf(

526:  function liquidateBorrow(

534:  function _addReserves(uint256 addAmount) external returns (uint256);

537:  contract CDelegationStorage {

544:  contract CDelegatorInterface is CDelegationStorage {

563:  contract CDelegateInterface is CDelegationStorage {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/Exponential.sol

```solidity
// @param and @return missing
26:  function getExp(
46:  function addExp(
58:  function subExp(
70:  function mulScalar(
85:  function mulScalarTruncate(
100:  function mulScalarTruncateAddUInt(
116:  function divScalar(
131:  function divScalarByExp(
154:  function divScalarByExpTruncate(
169:  function mulExp(
205:  function mulExp(
215:  function mulExp3(
232:  function divExp(
243:  function truncate(Exp memory exp) internal pure returns (uint) {
251:  function lessThanExp(
261:  function lessThanOrEqualExp(
271:  function isZeroExp(Exp memory value) internal pure returns (bool) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/EIP20Interface.sol

```solidity
9:  function name() external view returns (string memory);

11:  function symbol() external view returns (string memory);

13:  function decimals() external view returns (uint8);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/CarefulMath.sol

```solidity
// @param and @return missing
23:  function mulUInt(uint a, uint b) internal pure returns (MathError, uint) {
40:  function divUInt(uint a, uint b) internal pure returns (MathError, uint) {
51:  function subUInt(uint a, uint b) internal pure returns (MathError, uint) {
62:  function addUInt(uint a, uint b) internal pure returns (MathError, uint) {
75:  function addThenSubUInt(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Timelock.sol

```solidity
227:  constructor(address admin_, uint delay_) public {

243:  function setDelay(uint delay_) public {

261:  function acceptAdmin() public {

272:  function setPendingAdmin(address pendingAdmin_) public {

282:  function queueTransaction(

305:  function cancelTransaction(

323:  function executeTransaction(

373:  function getBlockTimestamp() internal view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernorBravoInterfaces.sol

```solidity
174:   contract GovernorBravoDelegateStorageV2 is GovernorBravoDelegateStorageV1 {

182:   interface TimelockInterface {

183:  function delay() external view returns (uint);

185:  function GRACE_PERIOD() external view returns (uint);

187:  function acceptAdmin() external;

189:  function queuedTransactions(bytes32 hash) external view returns (bool);

191:  function queueTransaction(

199:  function cancelTransaction(

207:  function executeTransaction(

216:  interface CompInterface {

217:  function getPriorVotes(

223:  interface GovernorAlpha {

// @return missing
225:  function proposalCount() external returns (uint);

```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernanceBravoDelegator.sol

```solidity
8:  contract GovernorBravoDelegator is

12:  constructor(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Comp.sol

```solidity
4:    contract Comp {

274:  function _delegate(address delegator, address delegatee) internal {

284:  function _transferTokens(address src, address dst, uint96 amount) internal {

309:  function _moveDelegates(

343:  function _writeCheckpoint(

367:  function safe32(

375:  function safe96(

383:  function add96(

393:  function sub96(

402:  function getChainId() internal pure returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/chainlink/AggregatorValidatorInterface.sol

```solidity
4:    interface AggregatorValidatorInterface {

5:    function validate(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/chainlink/AggregatorV3Interface.sol

```solidity
4:     interface AggregatorV3Interface {

5:  function decimals() external view returns (uint8);

7:  function description() external view returns (string memory);

9:  function version() external view returns (uint256);

11:  function getRoundData(

24:  function latestRoundData()
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol

```solidity
23:  function getUnderlyingPrice(address cToken) external view returns (uint256);

29:  function underlying() external view returns (address);

35:  function decimals() external view returns (uint8);

// @return missing
92:  function getUnderlyingPrice(

// @param missing
130:  function setPriceCap(

// @return missing
277:  function getChainlinkOraclePrice(

324:  function _min(uint256 a, uint256 b) internal pure returns (uint256) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```solidity
// @return missing
61:  function getUnderlyingPrice(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol

```solidity
27:  interface IOndoPriceOracle is PriceOracle {

28:  function setPrice(address fToken, uint256 price) external;

30:  function setFTokenToCToken(address fToken, address cToken) external;

32:  function setOracle(address newOracle) external;

69:   interface IOndoPriceOracleV2 is IOndoPriceOracle {

78:  function setPriceCap(address fToken, uint256 value) external;

80:  function setFTokenToChainlinkOracle(

85:  function setMaxChainlinkOracleTimeDelay(

89:  function setFTokenToOracleType(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol

```solidity
27:    interface IOndoPriceOracle is PriceOracle {

28:  function setPrice(address fToken, uint256 price) external;

30:  function setFTokenToCToken(address fToken, address cToken) external;

32:  function setOracle(address newOracle) external;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/CompoundLens.sol

```solidity
7:     interface ComptrollerLensInterface {

8:  function markets(address) external view returns (bool, uint);

10:  function oracle() external view returns (PriceOracle);

12:  function getAccountLiquidity(

16:  function getAssetsIn(address) external view returns (CToken[] memory);

19:   interface CErc20 {

20:  function underlying() external view returns (address);

23:  contract CompoundLens {

41:  function cTokenMetadata(

80:  function cTokenMetadataAll(

100:  function cTokenBalances(

129:  function cTokenBalancesAll(

146:  function cTokenUnderlyingPrice(

160:  function cTokenUnderlyingPriceAll(

179:  function getAccountLimits(

194:  function compareStrings(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol

```solidity
34:  function setKYCRequirementGroup(

40:  function setKYCRegistry(

46:  function initialize(

56:  function _beforeTokenTransfer(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol

```solidity
34:  function setKYCRequirementGroup(

40:  function setKYCRegistry(

46:  function initialize(

56:  function _beforeTokenTransfer(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol

```solidity
29:  function _beforeTokenTransfer(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol

```solidity
51:  constructor(

// @return missing
115:  function DOMAIN_SEPARATOR() external view returns (bytes32) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol

```solidity
// @return missing
27:  function kycRequirementGroup() external view returns (uint256);
30:  function kycRegistry() external view returns (IKYCRegistry);

// @param missing
33:  function setKYCRequirementGroup(uint256 group) external;
36:  function setKYCRegistry(address registry) external;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/ICashManager.sol

```solidity
// @params missing
19:  function requestMint(uint256 collateralIn) external;
22:  function claimMint(address user, uint256 epochToClaim) external;
32:  function setAssetRecipient(address _assetRecipient) external;
35:  function setFeeRecipient(address _feeRecipient) external;
38:  function setAssetSender(address newAssetSender) external;
41:  function setMinimumDepositAmount(uint256 _minimumDepositAmount) external;
44:  function setMintFee(uint256 _mintFee) external;
47:  function setMintExchangeRate(
53:  function setMintExchangeRateDeltaLimit(
58:  function requestRedemption(uint256 amountSharesTokenToRedeem) external;
62:  function completeRedemptions(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```solidity
53:  constructor(address _guardian) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```solidity
53:  constructor(address _guardian) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```solidity
53:  constructor(address _guardian) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/external/chainalysis/ISanctionsList.sol

```solidity
4:    interface ISanctionsList {

5:    function isSanctioned(address addr) external view returns (bool);
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol

```solidity
28:    contract CashManager is

127:  constructor(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol

```solidity
20:    contract TokenProxy is TransparentUpgradeableProxy {

21:  constructor(
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```solidity
90:  function transferTokens(

246:  function getBlockNumber() internal view virtual returns (uint) {

314:  function borrowBalanceStoredInternal(

367:  function exchangeRateStoredInternal() internal view virtual returns (uint) {

479:  function mintInternal(uint mintAmount) internal nonReentrant {

491:  function mintFresh(address minter, uint mintAmount) internal {

552:  function redeemInternal(uint redeemTokens) internal nonReentrant {

563:  function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {

576:  function redeemFresh(

669:  function borrowInternal(uint borrowAmount) internal nonReentrant {

679:  function borrowFresh(address payable borrower, uint borrowAmount) internal {

740:  function repayBorrowInternal(uint repayAmount) internal nonReentrant {

751:  function repayBorrowBehalfInternal(

767:  function repayBorrowFresh(

845:  function liquidateBorrowInternal(

870:  function liquidateBorrowFresh(

990:  function seizeInternal(

1414:  function getCashPrior() internal view virtual returns (uint);

1420:  function doTransferIn(

1430:  function doTransferOut(address payable to, uint amount) internal virtual;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol

```solidity
33:  uint internal constant borrowRateMaxMantissa = 0.0005e16;
36:  uint internal constant reserveFactorMaxMantissa = 1e18;
113:  uint public constant protocolSeizeShareMantissa = 1.75e16; //1.75%
164:  ISanctionsList public constant sanctionsList =
182:  bool public constant isCToken = true;

92:  mapping(address => uint) internal accountTokens;
95:  mapping(address => mapping(address => uint)) internal transferAllowances;
108:  mapping(address => BorrowSnapshot) internal accountBorrows;

// public and external functions should not be pre-fixed with underline
360:  function _setPendingAdmin(
364:  function _acceptAdmin() external virtual returns (uint);
366:  function _setComptroller(
370:  function _setReserveFactor(
374:  function _reduceReserves(uint reduceAmount) external virtual returns (uint);
376:  function _setInterestRateModel(
416:  function _addReserves(uint addAmount) external virtual returns (uint);
438:  function _setImplementation(
451:  function _becomeImplementation(bytes memory data) external virtual;
456:  function _resignImplementation() external virtual;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol

```solidity
// public and external functions should not be pre-fixed with underline
21:  function _becomeImplementation(bytes memory data) public virtual override {
39:  function _resignImplementation() public virtual override {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```solidity
// private and internal `functions` should preppend with `underline`
179:  function getCashPrior() internal view virtual override returns (uint) {
193:  function doTransferIn(
236:  function doTransferOut(

// public and external functions should not be pre-fixed with underline
168:  function _addReserves(uint addAmount) external override returns (uint) {
268:  function _delegateCompLikeTo(address compLikeDelegatee) external {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol

```solidity
// constant state variables should be UPPER_CASE.
10:  bool public constant isInterestRateModel = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol

```solidity
// constant state variables should be UPPER_CASE.
12:  uint constant expScale = 1e18;
13:  uint constant doubleScale = 1e36;
14:  uint constant halfExpScale = expScale / 2;
15:  uint constant mantissaOne = expScale;

// private and internal `functions` should preppend with `underline`
29:  function truncate(Exp memory exp) internal pure returns (uint) {
37:  function mul_ScalarTruncate(
48:  function mul_ScalarTruncateAddUInt(
60:  function lessThanExp(
70:  function lessThanOrEqualExp(
80:  function greaterThanExp(
90:  function isZeroExp(Exp memory value) internal pure returns (bool) {
94:  function safe224(
102:  function safe32(
110:  function add_(Exp memory a, Exp memory b) internal pure returns (Exp memory) {
202:  function fraction(uint a, uint b) internal pure returns (Double memory) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol

```solidity
// private and internal `functions` should preppend with `underline`
58:  function fail(Error err, FailureInfo info) internal returns (uint) {
67:  function failOpaque(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol

```solidity
6:   bool public constant isComptroller = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol


```solidity
//  private and internal `variables` should preppend with `underline`
//  constant state variables should be UPPER_CASE.
31:  uint internal constant borrowRateMaxMantissa = 0.0005e16;
34:  uint internal constant reserveFactorMaxMantissa = 1e18;

// private and internal `variables` should preppend with `underline`
57:  uint internal initialExchangeRateMantissa;
90:  mapping(address => uint) internal accountTokens;
93:  mapping(address => mapping(address => uint)) internal transferAllowances;
106:  mapping(address => BorrowSnapshot) internal accountBorrows;

```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```solidity
35:  uint internal constant borrowRateMaxMantissa = 0.0005e16;
38:  uint internal constant reserveFactorMaxMantissa = 1e18;

61:  uint internal initialExchangeRateMantissa;
94:  mapping(address => uint) internal accountTokens;
97:  mapping(address => mapping(address => uint)) internal transferAllowances;
110:  mapping(address => BorrowSnapshot) internal accountBorrows;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```solidity
// private and internal `functions` should preppend with `underline`
90:  function transferTokens(
246:  function getBlockNumber() internal view virtual returns (uint) {
314:  function borrowBalanceStoredInternal(
357:  function exchangeRateStoredInternal() internal view virtual returns (uint) {
479:  function mintInternal(uint mintAmount) internal nonReentrant {
491:  function mintFresh(address minter, uint mintAmount) internal {
552:  function redeemInternal(uint redeemTokens) internal nonReentrant {
563:  function redeemUnderlyingInternal(uint redeemAmount) internal nonReentrant {
576:  function redeemFresh(
669:  function borrowInternal(uint borrowAmount) internal nonReentrant {
679:  function borrowFresh(address payable borrower, uint borrowAmount) internal {
740:  function repayBorrowInternal(uint repayAmount) internal nonReentrant {
751:  function repayBorrowBehalfInternal(
767:  function repayBorrowFresh(
845:  function liquidateBorrowInternal(
870:  function liquidateBorrowFresh(
990:  function seizeInternal(
1411:  function getCashPrior() internal view virtual returns (uint);
1417:  function doTransferIn(
1427:  function doTransferOut(address payable to, uint amount) internal virtual;

// public and external functions should not be pre-fixed with underline
1060:  function _setPendingAdmin(
1085:  function _acceptAdmin() external override returns (uint) {
1112:  function _setComptroller(
1138:  function _setReserveFactor(
1239:  function _reduceReserves(
1300:  function _setInterestRateModel(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```solidity
// private and internal `functions` should preppend with `underline`
179:  function getCashPrior() internal view virtual override returns (uint) {
193:  function doTransferIn(
236:  function doTransferOut(

// public and external functions should not be pre-fixed with underline
168:  function _addReserves(uint addAmount) external override returns (uint) {
268:  function _delegateCompLikeTo(address compLikeDelegatee) external {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```solidity
// public and external functions should not be pre-fixed with underline
548:   function _setPendingAdmin(
552:  function _acceptAdmin() external returns (uint256);
554:  function _setComptroller(
558:  function _setReserveFactor(
562:  function _reduceReserves(uint256 reduceAmount) external returns (uint256);
564:  function _setInterestRateModel(
602:  function _addReserves(uint256 addAmount) external returns (uint256);
624:  function _setImplementation(
637:  function _becomeImplementation(bytes memory data) public;
642:  function _resignImplementation() public;
720:  function _setImplementation(
1099:  function _setPendingAdmin(
1113:  function _setComptroller(
1127:  function _setReserveFactor(
1144:  function _acceptAdmin() external returns (uint256) {
1156:  function _addReserves(uint256 addAmount) external returns (uint256) {
1168:  function _reduceReserves(uint256 reduceAmount) external returns (uint256) {
1181:  function _setInterestRateModel(


// private/internal `functions` & private/internal state variables should preppend with `underline`
208:  uint256 internal constant borrowRateMaxMantissa = 0.0005e16;
213:  uint256 internal constant reserveFactorMaxMantissa = 1e18;
238:  uint256 internal initialExchangeRateMantissa;
273:  mapping(address => uint256) internal accountTokens;
278:  mapping(address => mapping(address => uint256)) internal transferAllowances;
293:  mapping(address => BorrowSnapshot) internal accountBorrows;
1200:  function delegateTo(
```


https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/LinearTimelock.sol

```solidity
21:  mapping(address => InvestorParam) internal investorBalances;

122:  function safe32(

130:  function safe96(

138:  function add96(

148:  function sub96(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Unitroller.sol

```solidity
// public functions should not have a prefixed underline
40:  function _setPendingImplementation(
68:  function _acceptImplementation() public returns (uint) {
104:  function _setPendingAdmin(address newPendingAdmin) public returns (uint) {
128:  function _acceptAdmin() public returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/SafeMath.sol

```solidity
// private and internal `functions` should preppend with `underline`
28:  function add(uint256 a, uint256 b) internal pure returns (uint256) {
43:  function add(
62:  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
74:  function sub(
93:  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
115:  function mul(
159:  function div(
183:  function mod(uint256 a, uint256 b) internal pure returns (uint256) {
198:  function mod(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/PriceOracle.sol

```solidity
7:  bool public constant isPriceOracle = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/InterestRateModel.sol

```solidity
9:  bool public constant isInterestRateModel = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ExponentialNoError.sol

```solidity
// constant state variables should be UPPER_CASE
11:  uint constant expScale = 1e18;
12:  uint constant doubleScale = 1e36;
13:  uint constant halfExpScale = expScale / 2;
14:  uint constant mantissaOne = expScale;

28:  function truncate(Exp memory exp) internal pure returns (uint) {
36:  function mul_ScalarTruncate(
47:  function mul_ScalarTruncateAddUInt(
59:  function lessThanExp(
69:  function lessThanOrEqualExp(
79:  function greaterThanExp(
89:  function isZeroExp(Exp memory value) internal pure returns (bool) {
93:  function safe224(
242:  function fraction(uint a, uint b) internal pure returns (Double memory) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ErrorReporter.sol


```solidity
// private and internal `functions` should preppend with `underline`
57:  function fail(Error err, FailureInfo info) internal returns (uint) {
66:  function failOpaque(
194:  function fail(Error err, FailureInfo info) internal returns (uint) {
203:  function failOpaque(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerStorage.sol

```solidity
// public variables should not be pre-fixed with underline
83:  bool public _mintGuardianPaused;
84:  bool public _borrowGuardianPaused;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerInterface.sol

```solidity
5:  bool public constant isComptroller = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Comptroller.sol

```solidity
// constant state variables should be UPPER_CASE.
114:  uint224 public constant compInitialIndex = 1e36;
117:  uint internal constant closeFactorMinMantissa = 0.05e18; // 0.05
120:  uint internal constant closeFactorMaxMantissa = 0.9e18; // 0.9
123:  uint internal constant collateralFactorMaxMantissa = 0.98e18; // 0.98

// private and internal `functions` should preppend with `underline`
183:  function addToMarketInternal(
359:  function redeemAllowedInternal(
815:  function getAccountLiquidityInternal(
862:  function getHypotheticalAccountLiquidityInternal(
1401:  function adminOrInitializing() internal view returns (bool) {
1413:  function setCompSpeedInternal(
1450:  function updateCompSupplyIndex(address cToken) internal {
1479:  function updateCompBorrowIndex(
1514:  function distributeSupplierComp(address cToken, address supplier) internal {
1560:  function distributeBorrowerComp(
1688:  function grantCompInternal(

// public and external functions should not have a pre-fixed underline
1013:  function _setPriceOracle(PriceOracle newOracle) public returns (uint) {
1037:  function _setCloseFactor(
1057:  function _setCollateralFactor(
1122:  function _setLiquidationIncentive(
1155:  function _supportMarket(CToken cToken) external returns (uint) {
1223:  function _setMarketBorrowCaps(
1247:  function _setBorrowCapGuardian(address newBorrowCapGuardian) external {
1265:  function _setPauseGuardian(address newPauseGuardian) public returns (uint) {
1283:  function _setMintPaused(CToken cToken, bool state) public returns (bool) {
1299:  function _setBorrowPaused(CToken cToken, bool state) public returns (bool) {
1315:  function _setTransferPaused(bool state) public returns (bool) {
1327:  function _setSeizePaused(bool state) public returns (bool) {
1339:  function _become(Unitroller unitroller) public {
1709:  function _grantComp(address recipient, uint amount) public {
1722:  function _setCompSpeeds(
1745:  function _setContributorCompSpeed(

```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapLib.sol

```solidity
17:  function fraction(

26:  function decode112with18(uq112x112 memory self) internal pure returns (uint) {

41:  function currentBlockTimestamp() internal view returns (uint32) {

46:  function currentCumulativePrices(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapConfig.sol

```solidity
// constant state variables should be UPPER_CASE.
35:  uint public constant maxTokens = 25;


// from line 40 to line 298 internal variables should be pre-fixed with underline
  address internal immutable cToken00;
  address internal immutable cToken01;
  address internal immutable cToken02;
  address internal immutable cToken03;
  address internal immutable cToken04;
  address internal immutable cToken05;
  address internal immutable cToken06;
  address internal immutable cToken07;
  address internal immutable cToken08;
  address internal immutable cToken09;
  address internal immutable cToken10;
  address internal immutable cToken11;
  address internal immutable cToken12;
  address internal immutable cToken13;
  address internal immutable cToken14;
  address internal immutable cToken15;
  address internal immutable cToken16;
  address internal immutable cToken17;
  address internal immutable cToken18;
  address internal immutable cToken19;
  address internal immutable cToken20;
  address internal immutable cToken21;
  address internal immutable cToken22;
  address internal immutable cToken23;
  address internal immutable cToken24;

  address internal immutable underlying00;
  address internal immutable underlying01;
  address internal immutable underlying02;
  address internal immutable underlying03;
  address internal immutable underlying04;
  address internal immutable underlying05;
  address internal immutable underlying06;
  address internal immutable underlying07;
  address internal immutable underlying08;
  address internal immutable underlying09;
  address internal immutable underlying10;
  address internal immutable underlying11;
  address internal immutable underlying12;
  address internal immutable underlying13;
  address internal immutable underlying14;
  address internal immutable underlying15;
  address internal immutable underlying16;
  address internal immutable underlying17;
  address internal immutable underlying18;
  address internal immutable underlying19;
  address internal immutable underlying20;
  address internal immutable underlying21;
  address internal immutable underlying22;
  address internal immutable underlying23;
  address internal immutable underlying24;

  bytes32 internal immutable symbolHash00;
  bytes32 internal immutable symbolHash01;
  bytes32 internal immutable symbolHash02;
  bytes32 internal immutable symbolHash03;
  bytes32 internal immutable symbolHash04;
  bytes32 internal immutable symbolHash05;
  bytes32 internal immutable symbolHash06;
  bytes32 internal immutable symbolHash07;
  bytes32 internal immutable symbolHash08;
  bytes32 internal immutable symbolHash09;
  bytes32 internal immutable symbolHash10;
  bytes32 internal immutable symbolHash11;
  bytes32 internal immutable symbolHash12;
  bytes32 internal immutable symbolHash13;
  bytes32 internal immutable symbolHash14;
  bytes32 internal immutable symbolHash15;
  bytes32 internal immutable symbolHash16;
  bytes32 internal immutable symbolHash17;
  bytes32 internal immutable symbolHash18;
  bytes32 internal immutable symbolHash19;
  bytes32 internal immutable symbolHash20;
  bytes32 internal immutable symbolHash21;
  bytes32 internal immutable symbolHash22;
  bytes32 internal immutable symbolHash23;
  bytes32 internal immutable symbolHash24;

  uint256 internal immutable baseUnit00;
  uint256 internal immutable baseUnit01;
  uint256 internal immutable baseUnit02;
  uint256 internal immutable baseUnit03;
  uint256 internal immutable baseUnit04;
  uint256 internal immutable baseUnit05;
  uint256 internal immutable baseUnit06;
  uint256 internal immutable baseUnit07;
  uint256 internal immutable baseUnit08;
  uint256 internal immutable baseUnit09;
  uint256 internal immutable baseUnit10;
  uint256 internal immutable baseUnit11;
  uint256 internal immutable baseUnit12;
  uint256 internal immutable baseUnit13;
  uint256 internal immutable baseUnit14;
  uint256 internal immutable baseUnit15;
  uint256 internal immutable baseUnit16;
  uint256 internal immutable baseUnit17;
  uint256 internal immutable baseUnit18;
  uint256 internal immutable baseUnit19;
  uint256 internal immutable baseUnit20;
  uint256 internal immutable baseUnit21;
  uint256 internal immutable baseUnit22;
  uint256 internal immutable baseUnit23;
  uint256 internal immutable baseUnit24;

  PriceSource internal immutable priceSource00;
  PriceSource internal immutable priceSource01;
  PriceSource internal immutable priceSource02;
  PriceSource internal immutable priceSource03;
  PriceSource internal immutable priceSource04;
  PriceSource internal immutable priceSource05;
  PriceSource internal immutable priceSource06;
  PriceSource internal immutable priceSource07;
  PriceSource internal immutable priceSource08;
  PriceSource internal immutable priceSource09;
  PriceSource internal immutable priceSource10;
  PriceSource internal immutable priceSource11;
  PriceSource internal immutable priceSource12;
  PriceSource internal immutable priceSource13;
  PriceSource internal immutable priceSource14;
  PriceSource internal immutable priceSource15;
  PriceSource internal immutable priceSource16;
  PriceSource internal immutable priceSource17;
  PriceSource internal immutable priceSource18;
  PriceSource internal immutable priceSource19;
  PriceSource internal immutable priceSource20;
  PriceSource internal immutable priceSource21;
  PriceSource internal immutable priceSource22;
  PriceSource internal immutable priceSource23;
  PriceSource internal immutable priceSource24;

  uint256 internal immutable fixedPrice00;
  uint256 internal immutable fixedPrice01;
  uint256 internal immutable fixedPrice02;
  uint256 internal immutable fixedPrice03;
  uint256 internal immutable fixedPrice04;
  uint256 internal immutable fixedPrice05;
  uint256 internal immutable fixedPrice06;
  uint256 internal immutable fixedPrice07;
  uint256 internal immutable fixedPrice08;
  uint256 internal immutable fixedPrice09;
  uint256 internal immutable fixedPrice10;
  uint256 internal immutable fixedPrice11;
  uint256 internal immutable fixedPrice12;
  uint256 internal immutable fixedPrice13;
  uint256 internal immutable fixedPrice14;
  uint256 internal immutable fixedPrice15;
  uint256 internal immutable fixedPrice16;
  uint256 internal immutable fixedPrice17;
  uint256 internal immutable fixedPrice18;
  uint256 internal immutable fixedPrice19;
  uint256 internal immutable fixedPrice20;
  uint256 internal immutable fixedPrice21;
  uint256 internal immutable fixedPrice22;
  uint256 internal immutable fixedPrice23;
  uint256 internal immutable fixedPrice24;

  address internal immutable uniswapMarket00;
  address internal immutable uniswapMarket01;
  address internal immutable uniswapMarket02;
  address internal immutable uniswapMarket03;
  address internal immutable uniswapMarket04;
  address internal immutable uniswapMarket05;
  address internal immutable uniswapMarket06;
  address internal immutable uniswapMarket07;
  address internal immutable uniswapMarket08;
  address internal immutable uniswapMarket09;
  address internal immutable uniswapMarket10;
  address internal immutable uniswapMarket11;
  address internal immutable uniswapMarket12;
  address internal immutable uniswapMarket13;
  address internal immutable uniswapMarket14;
  address internal immutable uniswapMarket15;
  address internal immutable uniswapMarket16;
  address internal immutable uniswapMarket17;
  address internal immutable uniswapMarket18;
  address internal immutable uniswapMarket19;
  address internal immutable uniswapMarket20;
  address internal immutable uniswapMarket21;
  address internal immutable uniswapMarket22;
  address internal immutable uniswapMarket23;
  address internal immutable uniswapMarket24;

  address internal immutable reporter00;
  address internal immutable reporter01;
  address internal immutable reporter02;
  address internal immutable reporter03;
  address internal immutable reporter04;
  address internal immutable reporter05;
  address internal immutable reporter06;
  address internal immutable reporter07;
  address internal immutable reporter08;
  address internal immutable reporter09;
  address internal immutable reporter10;
  address internal immutable reporter11;
  address internal immutable reporter12;
  address internal immutable reporter13;
  address internal immutable reporter14;
  address internal immutable reporter15;
  address internal immutable reporter16;
  address internal immutable reporter17;
  address internal immutable reporter18;
  address internal immutable reporter19;
  address internal immutable reporter20;
  address internal immutable reporter21;
  address internal immutable reporter22;
  address internal immutable reporter23;
  address internal immutable reporter24;

  uint256 internal immutable reporterMultiplier00;
  uint256 internal immutable reporterMultiplier01;
  uint256 internal immutable reporterMultiplier02;
  uint256 internal immutable reporterMultiplier03;
  uint256 internal immutable reporterMultiplier04;
  uint256 internal immutable reporterMultiplier05;
  uint256 internal immutable reporterMultiplier06;
  uint256 internal immutable reporterMultiplier07;
  uint256 internal immutable reporterMultiplier08;
  uint256 internal immutable reporterMultiplier09;
  uint256 internal immutable reporterMultiplier10;
  uint256 internal immutable reporterMultiplier11;
  uint256 internal immutable reporterMultiplier12;
  uint256 internal immutable reporterMultiplier13;
  uint256 internal immutable reporterMultiplier14;
  uint256 internal immutable reporterMultiplier15;
  uint256 internal immutable reporterMultiplier16;
  uint256 internal immutable reporterMultiplier17;
  uint256 internal immutable reporterMultiplier18;
  uint256 internal immutable reporterMultiplier19;
  uint256 internal immutable reporterMultiplier20;
  uint256 internal immutable reporterMultiplier21;
  uint256 internal immutable reporterMultiplier22;
  uint256 internal immutable reporterMultiplier23;
  uint256 internal immutable reporterMultiplier24;

  bool internal immutable isUniswapReversed00;
  bool internal immutable isUniswapReversed01;
  bool internal immutable isUniswapReversed02;
  bool internal immutable isUniswapReversed03;
  bool internal immutable isUniswapReversed04;
  bool internal immutable isUniswapReversed05;
  bool internal immutable isUniswapReversed06;
  bool internal immutable isUniswapReversed07;
  bool internal immutable isUniswapReversed08;
  bool internal immutable isUniswapReversed09;
  bool internal immutable isUniswapReversed10;
  bool internal immutable isUniswapReversed11;
  bool internal immutable isUniswapReversed12;
  bool internal immutable isUniswapReversed13;
  bool internal immutable isUniswapReversed14;
  bool internal immutable isUniswapReversed15;
  bool internal immutable isUniswapReversed16;
  bool internal immutable isUniswapReversed17;
  bool internal immutable isUniswapReversed18;
  bool internal immutable isUniswapReversed19;
  bool internal immutable isUniswapReversed20;
  bool internal immutable isUniswapReversed21;
  bool internal immutable isUniswapReversed22;
  bool internal immutable isUniswapReversed23;
  bool internal immutable isUniswapReversed24;
  
  
569:  function get(

589:  function getReporterIndex(address reporter) internal view returns (uint) {

619:  function getCTokenIndex(address cToken) internal view returns (uint) {

649:  function getUnderlyingIndex(address underlying) internal view returns (uint) {

679:  function getSymbolHashIndex(bytes32 symbolHash) internal view returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapAnchoredView.sol

```solidity
29:  uint public constant ethBaseUnit = 1e18;
32:  uint public constant expScale = 1e18;
81:  bytes32 constant ethHash = keccak256(abi.encodePacked("ETH"));

153:  function priceInternal(

237:  function calculateAnchorPriceFromEthPrice(

258:  function convertReportedPrice(

269:  function isWithinAnchor(

285:  function currentCumulativePrice(

301:  function fetchEthAnchorPrice() internal returns (uint) {

314:  function fetchAnchorPrice(

368:  function pokeWindowValues(

421:  function mul(uint a, uint b) internal pure returns (uint) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cToken.sol

```solidity
// constant state variables should be UPPER_CASE
22:  bool public constant isCToken = true;
45:  uint constant borrowRateMaxMantissa = 5e14;
50:  uint constant reserveFactorMaxMantissa = 1e18;

// just pre-fix underline on private and internal functions
1811:  function _setPendingAdmin(
1837:  function _acceptAdmin() external returns (uint) {
1865:  function _setComptroller(
1891:  function _setReserveFactor(
1949:  function _reduceReserves(
2030:  function _setInterestRateModel(

// private and internal `functions` should preppend with `underline`
283:  function transferTokens(
475:  function getBlockNumber() internal view returns (uint) {
595:  function borrowBalanceStoredInternal(
663:  function exchangeRateStoredInternal()
864:  function mintInternal(uint mintAmount) internal nonReentrant returns (uint) {
890:  function mintFresh(address minter, uint mintAmount) internal returns (uint) {
1006:  function redeemInternal(
1024:  function redeemUnderlyingInternal(
1054:  function redeemFresh(
1219:  function borrowInternal(
1244:  function borrowFresh(
1355:  function repayBorrowInternal(
1374:  function repayBorrowBehalfInternal(
1405:  function repayBorrowFresh(
1541:  function liquidateBorrowInternal(
1577:  function liquidateBorrowFresh(
2102:  function getCashPrior() internal view returns (uint);
2108:  function checkTransferIn(
2118:  function doTransferIn(address from, uint amount) internal returns (Error);
2125:  function doTransferOut(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cErc20Delegator.sol

```solidity
208:  uint256 internal constant borrowRateMaxMantissa = 0.0005e16;

213:  uint256 internal constant reserveFactorMaxMantissa = 1e18;

300:  bool public constant isCToken = true;

238:  uint256 internal initialExchangeRateMantissa;

273:  mapping(address => uint256) internal accountTokens;

278:  mapping(address => mapping(address => uint256)) internal transferAllowances;

293:  mapping(address => BorrowSnapshot) internal accountBorrows;

1127:  function delegateTo(


// just pre-fix underline on private and internal functions
480:  function _setPendingAdmin(
484:  function _acceptAdmin() external returns (uint256);
486:  function _setComptroller(
490:  function _setReserveFactor(
494:  function _reduceReserves(uint256 reduceAmount) external returns (uint256);
496:  function _setInterestRateModel(
534:  function _addReserves(uint256 addAmount) external returns (uint256);
556:  function _setImplementation(
569:  function _becomeImplementation(bytes memory data) public;
574:  function _resignImplementation() public;
647:  function _setImplementation(
1026:  function _setPendingAdmin(
1040:  function _setComptroller(
1054:  function _setReserveFactor(
1071:  function _acceptAdmin() external returns (uint256) {
1083:  function _addReserves(uint256 addAmount) external returns (uint256) {
1095:  function _reduceReserves(uint256 reduceAmount) external returns (uint256) {
1108:  function _setInterestRateModel(

```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/LegacyInterestRateModel.sol

```solidity
9:  bool public constant isInterestRateModel = true;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/Exponential.sol

```solidity
// constant state variables should be UPPER_CASE.
13:  uint constant expScale = 1e18;
14:  uint constant halfExpScale = expScale / 2;
15:  uint constant mantissaOne = expScale;

26:  function getExp(

46:  function addExp(

58:  function subExp(

70:  function mulScalar(

85:  function mulScalarTruncate(

100:  function mulScalarTruncateAddUInt(

116:  function divScalar(

131:  function divScalarByExp(

154:  function divScalarByExpTruncate(

169:  function mulExp(

205:  function mulExp(

215:  function mulExp3(

232:  function divExp(

243:  function truncate(Exp memory exp) internal pure returns (uint) {

251:  function lessThanExp(

261:  function lessThanOrEqualExp(

271:  function isZeroExp(Exp memory value) internal pure returns (bool) {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/CarefulMath.sol

```solidity
23:  function mulUInt(uint a, uint b) internal pure returns (MathError, uint) {

40:  function divUInt(uint a, uint b) internal pure returns (MathError, uint) {

51:  function subUInt(uint a, uint b) internal pure returns (MathError, uint) {

62:  function addUInt(uint a, uint b) internal pure returns (MathError, uint) {

75:  function addThenSubUInt(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Timelock.sol

```solidity
36:  function add(uint256 a, uint256 b) internal pure returns (uint256) {

52:  function sub(uint256 a, uint256 b) internal pure returns (uint256) {

68:  function sub(

88:  function mul(uint256 a, uint256 b) internal pure returns (uint256) {

113:  function div(uint256 a, uint256 b) internal pure returns (uint256) {

130:  function div(

154:  function mod(uint256 a, uint256 b) internal pure returns (uint256) {

172:  function mod(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernanceBravoDelegator.sol

```solidity
// public functions should not be pre-fixed with underline
45:  function _setImplementation(address implementation_) public {

67:  function delegateTo(address callee, bytes memory data) internal {
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Comp.sol

```solidity
6:  string public constant name = "Compound";

9:  string public constant symbol = "COMP";

12:  uint8 public constant decimals = 18;

15:  uint public constant totalSupply = 10000000e18; // 10 million Comp

18:  mapping(address => mapping(address => uint96)) internal allowances;

21:  mapping(address => uint96) internal balances;

367:  function safe32(

375:  function safe96(

393:  function sub96(

402:  function getChainId() internal pure returns (uint) {
```


https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```solidity
// constant state variables should be UPPER_CASE
29:  uint public constant blocksPerYear = 2628000;

// private and internal `variables` should preppend with `underline`
171:  function updateJumpRateModelInternal(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/CompoundLens.sol

```solidity
194:  function compareStrings(
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol

```solidity
48:  address internal immutable guardian;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol

```solidity
48:  address internal immutable guardian;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol

```solidity
48:  address internal immutable guardian;
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ExponentialNoError.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/EIP20Interface.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20Delegate/CTokenInterfaces.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol

```solidity
7:  pragma solidity ^0.5.12;
138:  pragma solidity ^0.5.12;
181:  pragma solidity ^0.5.12;
296:  pragma solidity ^0.5.16;
302:  pragma solidity ^0.5.16;
324:  pragma solidity ^0.5.16;
647:  pragma solidity ^0.5.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/test/upgrades/IMulticall.sol

```solidity
16:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/test/upgrades/CTokenDelegateUpgrade.sol

```solidity
2:  pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/test/upgrades/CCashDelegateUpgrade.sol

```solidity
2: pragma solidity ^0.8.10;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/LinearTimelock.sol

```solidity
2:  pragma solidity >=0.8.3;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/ondo/ondo-token/IOndo.sol

```solidity
2: pragma solidity >=0.8.3;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/SafeMath.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ReentrancyGuard.sol

```solidity
1:  pragma solidity ^0.5.8;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/PriceOracle.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Ownable.sol

```solidity
2:  pragma solidity ^0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/InterestRateModel.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ExponentialNoError.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ErrorReporter.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerStorage.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/ComptrollerInterface.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/Comptroller.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapLib.sol

```solidity
3:  pragma solidity ^0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapConfig.sol

```solidity
3:  pragma solidity ^0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/uniswap/UniswapAnchoredView.sol

```solidity
6:  pragma solidity ^0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cToken.sol

```solidity
1:   pragma solidity ^0.5.8;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/cErc20Delegator.sol

```solidity
7:    pragma solidity ^0.5.12;

138:  pragma solidity ^0.5.12;

181:  pragma solidity ^0.5.12;

579:  pragma solidity ^0.5.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/LegacyInterestRateModel.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/Exponential.sol

```solidity
1:   pragma solidity ^0.5.8;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/EIP20Interface.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/tokens/CarefulMath.sol

```solidity
1:  pragma solidity ^0.5.8;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Timelock.sol

```solidity
7:   pragma solidity ^0.5.8;

184: pragma solidity ^0.5.8;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernorBravoInterfaces.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/GovernanceBravoDelegator.sol

```solidity
1: pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Comp.sol

```solidity
1:  pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/chainlink/AggregatorValidatorInterface.sol

```solidity
2:   pragma solidity ^0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol

```solidity
15:  pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol

```solidity
1:   pragma solidity ^0.5.16;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol

```solidity
15:  pragma solidity 0.6.12;
```

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/CompoundLens.sol

```solidity
1:  pragma solidity ^0.5.16;
```

---

### Observations [6]

```solidity
// add this literal address to immutable or constant address
1794: function getCompAddress() public view returns (address) {
    return 0xfAbA6f8e4a5E8Ab82F62fe7C39859FA577269BE3;
  }
```
