# Low

## `CashKYCSender`: KYC check may lead to unrecoverable assets

The `CashKYCSender` token applies a KYC check to the caller and `from` address in its [`_beforeTokenTransfer`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L56-L75) function, but not to the `to` address:

```solidity
  function _beforeTokenTransfer(
    address from,
    address to,
    uint256 amount
  ) internal override {
    super._beforeTokenTransfer(from, to, amount);

    require(
      _getKYCStatus(_msgSender()),
      "CashKYCSender: must be KYC'd to initiate transfer"
    );

    if (from != address(0)) {
      // Only check KYC if not minting
      require(
        _getKYCStatus(from),
        "CashKYCSender: `from` address must be KYC'd to send tokens"
      );
    }
  }
```

This means it's possible for a KYC'd owner of a `CashKYCSender` token to lose access to their assets by transferring them to a contract address that does not represent a human and cannot KYC, like a smart wallet, vault contract, or noncustodial DeFi protocol. Once tokens are transferred in, the smart contract will be blocked from initiating token transfers out. The composability of ERC20 tokens and protocols that interact with them makes it especially easy for users to make this mistake.

Recommendation: Since KYC checks break composability with other protocols, prevent all transfers to non-KYC'd addresses (i.e., use `CashKYCSenderReceiver` for all tokens).


## `OndoPriceOracleV2`: Oracle cannot support tokens with more than 28 decimals

`OndoPriceOracleV2` calculates a `scaleFactor` by subtracting the collateral token's `decimals()` and the configured Chainlink price feed's `decimals()` from 36. Since Chainlink USD price feeds use 8 decimals, this calculation will revert with a checked arithmetic error if a collateral token has more than 28 decimals:

[`OndoPriceOracleV2`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L260-L263):

```solidity
    fTokenToChainlinkOracle[fToken].scaleFactor = (10 **
      (36 -
        uint256(IERC20Like(underlying).decimals()) -
        uint256(AggregatorV3Interface(chainlinkOracle).decimals())));
```

Currently all Chainlink USD price feeds use 8 decimals, and none of Ondo's supported collateral tokens have more than 18 decimals, so this is very unlikely but theoretically possible if an ETH-denominated price feed or high decimal denomination collateral token are used in the future.

# Noncritical

## `CashManager`: Fee-on-transfer collateral will overstate deposit value

[`CashManager#requestMint`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L195-L230) transfers a total amount of collateral token exactly equal to the `collateralAmountIn` provided by the caller:

```solidity
  function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
    if (collateralAmountIn < minimumDepositAmount) {
      revert MintRequestAmountTooSmall();
    }

    uint256 feesInCollateral = _getMintFees(collateralAmountIn);
    uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral;

    _checkAndUpdateMintLimit(depositValueAfterFees);

    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);
    collateral.safeTransferFrom(
      msg.sender,
      assetRecipient,
      depositValueAfterFees
    );

    mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;

    emit MintRequested(
      msg.sender,
      currentEpoch,
      collateralAmountIn,
      depositValueAfterFees,
      feesInCollateral
    );
  }
```

Howver, if the collateral asset is a fee-on-transfer token:

- The `feeRecipient` will receive an amount less than `feesInCollateral`.
- The `assetRecipient` will receive an amount less than `depositValueAfterFees`.
- The user's deposited collateral amount stored in `mintRequestsPerEpoch` will be greater than the actual amount of collateral recieved by the `assetRecipient`.

Among Ondo's supported collateral tokens, the USDT contract supports an optional fee on transfer that could be enabled by the contract owner at any time. Since exchange rates are calculated off chain by a trusted third party, and fees would be applied equally to all transfers, this is more of a theoretical gotcha than a major incompatibility—transfers would still succeed, but the recorded deposit value would be overstated.

## `CashManager`: `mintRequestsPerEpoch` is overloaded

The meaning of the `mintRequestsPerEpoch` mapping is overloaded. It represents both the amount of a user's deposit in [`requestMint`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L221):

```solidity
    mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
```

...and is also used to track whether a user has claimed their cash tokens for a given epoch in [`claimMint`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L259-L260):

```solidity
    mintRequestsPerEpoch[epochToClaim][user] = 0;
    cash.mint(user, cashOwed);
```

However, this has some drawbacks: since this value is set to zero when claimed, the history of deposits is not available on chain, and it may be unclear whether a user has deposited and claimed during a given epoch or has not deposited at all. Consider tracking whether a user has claimed tokens for a given epoch in a separate mapping rather than overloading the meaning of this mapping value.

## `CashManager`: Avoid storing old parameter values in temp variables

Throughout the codebase, most setter functions that update parameter values write the previous value to a temporary variable, then read it when emitting an event. For example, the [`setRedeemMinimum`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L816-L822) function:

```solidity
 function setRedeemMinimum(
    uint256 newRedeemMinimum
  ) external onlyRole(MANAGER_ADMIN) {
    uint256 oldRedeemMin = minimumRedeemAmount;
    minimumRedeemAmount = newRedeemMinimum;
    emit MinimumRedeemAmountSet(oldRedeemMin, minimumRedeemAmount);
  }
```

This three-step pattern can be simplified by emitting the event before writing the new value to storage, eliminating the need for a temporary variable. This saves gas in most cases and simplifies the code:

```solidity
 function setRedeemMinimum(
    uint256 newRedeemMinimum
  ) external onlyRole(MANAGER_ADMIN) {
    emit MinimumRedeemAmountSet(minimumRedeemAmount, newRedeemMinimum);
    minimumRedeemAmount = newRedeemMinimum;
  }
```

## `CashManager`: Follow checks-effects-interactions in `requestMint`

[`CashManager#requestMint`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L195-L230) updates the `mintRequestsPerEpoch` mapping in storage after performing token transfers:

```solidity
  function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
    if (collateralAmountIn < minimumDepositAmount) {
      revert MintRequestAmountTooSmall();
    }

    uint256 feesInCollateral = _getMintFees(collateralAmountIn);
    uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral;

    _checkAndUpdateMintLimit(depositValueAfterFees);

    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);
    collateral.safeTransferFrom(
      msg.sender,
      assetRecipient,
      depositValueAfterFees
    );

    mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;

    emit MintRequested(
      msg.sender,
      currentEpoch,
      collateralAmountIn,
      depositValueAfterFees,
      feesInCollateral
    );
  }
```

Since this function is protected by a reentrancy guard, following the [checks-effects-interactions pattern](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html) is a matter of style more than security, but it would be more idiomatic to perform all state changes before making external calls.

Suggested:

```solidity
  function requestMint(
    uint256 collateralAmountIn
  )
    external
    override
    updateEpoch
    nonReentrant
    whenNotPaused
    checkKYC(msg.sender)
  {
    if (collateralAmountIn < minimumDepositAmount) {
      revert MintRequestAmountTooSmall();
    }

    uint256 feesInCollateral = _getMintFees(collateralAmountIn);
    uint256 depositValueAfterFees = collateralAmountIn - feesInCollateral;

    _checkAndUpdateMintLimit(depositValueAfterFees);

    mintRequestsPerEpoch[currentEpoch][msg.sender] += depositValueAfterFees;
    emit MintRequested(
      msg.sender,
      currentEpoch,
      collateralAmountIn,
      depositValueAfterFees,
      feesInCollateral
    );

    collateral.safeTransferFrom(msg.sender, feeRecipient, feesInCollateral);
    collateral.safeTransferFrom(
      msg.sender,
      assetRecipient,
      depositValueAfterFees
    );
  }
```

## `CashManager`: Rely on default value for `mintFee`

It's not necessary to explicitly set [`mintFee`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L63-L64) to zero:

```solidity
  // Minting fee specified in basis points
  uint256 public mintFee = 0;
```

Consider relying on the default value of zero instead:

```solidity
  // Minting fee specified in basis points
  uint256 public mintFee;
```

## `CashManager`: Set `minimumDepositAmount` to `BPS_DENOMINATOR`

The [`minimumDepositAmount`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L55-L58) parameter stored in `CashManager` is set to a default value of `10_000` in order to ensure it's greater than the `BPS_DENOMINATOR`:

```solidity
  /// @dev Mint/Redeem Parameters
  // Minimum amount that must be deposited to mint CASH
  // Denoted in decimals of `collateral`
  uint256 public minimumDepositAmount = 10_000;
```

However, the significance of this value is unclear. Consider setting this value to `BPS_DENOMINATOR` rather than the magic number `10_000`:

```solidity
  /// @dev Mint/Redeem Parameters
  // Minimum amount that must be deposited to mint CASH
  // Denoted in decimals of `collateral`
  uint256 public minimumDepositAmount = BPS_DENOMINATOR;
```

## `CashManager`: Incomplete comment on `_getMintFees`

The natspec description of [`_getMintFees`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L496)
is incomplete:

```solidity
   * @notice Given amount of `collateral`, returns how
```

## `Proxy`, `CashKYCSender`, `CashKYCSenderReceiver`: Shadowed variable names

`_admin` and `_data` are shadowed in [`Proxy`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/Proxy.sol#L25-L26):

```solidity
  ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
```

`name`, `symbol`, `kycRegistry`, `kycRequirementGroup` are shadowed in [`CashKYCSender`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSender.sol#L52-L53):

```solidity
    __ERC20PresetMinterPauser_init(name, symbol);
    __KYCRegistryClientInitializable_init(kycRegistry, kycRequirementGroup);
```

...and in [`CashKYCSenderReceiver`](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/token/CashKYCSenderReceiver.sol#L52-L53):

```solidity
    __ERC20PresetMinterPauser_init(name, symbol);
    __KYCRegistryClientInitializable_init(kycRegistry, kycRequirementGroup);
```
## `KYCRegistryClientConstructable`: Duplicate import

`KYCRegistryClient` is imported twice in [KYCRegistryClientConstructable](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17-L22):

```solidity
import "contracts/cash/kyc/KYCRegistryClient.sol";

pragma solidity 0.8.16;

import "contracts/cash/kyc/KYCRegistryClient.sol";
```
