# Gas Findings

## GAS-01

Caching `lastSetMintExchangeRate`  (6 readings are removed)

```markdown
contracts/cash/CashManager.sol | setMintExchangeRate(…)
```

Before:

```solidity
uint256 rateDifference;
if (exchangeRate > lastSetMintExchangeRate) {
  rateDifference = exchangeRate - lastSetMintExchangeRate;
} else if (exchangeRate < lastSetMintExchangeRate) {
  rateDifference = lastSetMintExchangeRate - exchangeRate;
}

uint256 maxDifferenceThisEpoch = (lastSetMintExchangeRate *
  exchangeRateDeltaLimit) / BPS_DENOMINATOR;

if (rateDifference > maxDifferenceThisEpoch) {
  epochToExchangeRate[epochToSet] = exchangeRate;
  _pause();
  emit MintExchangeRateCheckFailed(
    epochToSet,
    lastSetMintExchangeRate,
    exchangeRate
  );
} else {
  uint256 oldExchangeRate = lastSetMintExchangeRate;
  epochToExchangeRate[epochToSet] = exchangeRate;
  lastSetMintExchangeRate = exchangeRate;
  emit MintExchangeRateSet(epochToSet, oldExchangeRate, exchangeRate);
}
```

After:

```solidity
uint256 rateDifference;
uint256 lastSetMintExchangeRate_ = lastSetMintExchangeRate;

if (exchangeRate > lastSetMintExchangeRate_) {
    rateDifference = exchangeRate - lastSetMintExchangeRate_;
} else if (exchangeRate < lastSetMintExchangeRate_) {
    rateDifference = lastSetMintExchangeRate_ - exchangeRate;
}

uint256 maxDifferenceThisEpoch = (lastSetMintExchangeRate_ *
    exchangeRateDeltaLimit) / BPS_DENOMINATOR;

if (rateDifference > maxDifferenceThisEpoch) {
    epochToExchangeRate[epochToSet] = exchangeRate;
    _pause();
    emit MintExchangeRateCheckFailed(
        epochToSet,
        lastSetMintExchangeRate_,
        exchangeRate
    );
} else {
    epochToExchangeRate[epochToSet] = exchangeRate;
    lastSetMintExchangeRate = exchangeRate;
    emit MintExchangeRateSet(epochToSet, lastSetMintExchangeRate_, exchangeRate);
}
```

## GAS-02

Do not repeat code. Move out of the IF statement the common code between both branches. (Savings of 5 gas units per call)

```markdown
contracts/cash/CashManager.sol | setMintExchangeRate(…)
```

Before:

```solidity
if (rateDifference > maxDifferenceThisEpoch) {
		epochToExchangeRate[epochToSet] = exchangeRate;
    _pause();
    emit MintExchangeRateCheckFailed(
        epochToSet,
        lastSetMintExchangeRate,
        exchangeRate
    );
} else {
    uint256 oldExchangeRate = lastSetMintExchangeRate;
    epochToExchangeRate[epochToSet] = exchangeRate;
    lastSetMintExchangeRate = exchangeRate;
    emit MintExchangeRateSet(epochToSet, oldExchangeRate, exchangeRate);
}
```

After:

```solidity
epochToExchangeRate[epochToSet] = exchangeRate;
if (rateDifference > maxDifferenceThisEpoch) {
    _pause();
    emit MintExchangeRateCheckFailed(
        epochToSet,
        lastSetMintExchangeRate,
        exchangeRate
    );
} else {
    uint256 oldExchangeRate = lastSetMintExchangeRate;
    lastSetMintExchangeRate = exchangeRate;
    emit MintExchangeRateSet(epochToSet, oldExchangeRate, exchangeRate);
}
```

# Informationals

## I-01

Inconsistency between the comment content, the code, and the corresponding test.

```solidity
// contracts/cash/CashManager.sol | setMintFee(…)

/**
 * @notice Sets mint fee
 *
 * @param _mintFee new mint fee specified in basis points
 *
 * @dev The maximum fee that can be set is 10_000 bps, or 100%
 */
function setMintFee(uint256 _mintFee)
    external
    override
    onlyRole(MANAGER_ADMIN)
{
    if (_mintFee >= BPS_DENOMINATOR) { // If _mintFee is set to 10_000 this fail.
        revert MintFeeTooLarge();
    }
    uint256 oldMintFee = mintFee;
    mintFee = _mintFee;
    emit MintFeeSet(oldMintFee, _mintFee);
}

// forge-tests/cash/cash_manager/Setters.t.sol | test_setMintFee_fail_tooLarge(...)

function test_setMintFee_fail_tooLarge() public {
  vm.expectRevert(ICashManager.MintFeeTooLarge.selector);
  cashManager.setMintFee(10000);// This confirms that it is intended to fail        // if ***_mintFee*** is set at ***10_000***. 
																// So either the comment is wrong or 
																// the code doesn't match the original intent of the comment.
}
```