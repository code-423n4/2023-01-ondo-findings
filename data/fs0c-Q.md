## [QA-01] lack of storage `_gap` in upgradable contracts.

For upgradeable contracts, there must be storage gap to “allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments”. Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: [https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable)

### POC

[contracts/cash/token/Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)

[contracts/cash/token/CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)

[contracts/cash/token/CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

### Recommendation

Add the following to the contracts.

```solidity
uint256[50] private __gap;
```

## [QA-02] `minimumRedeemAmount` can be max without checks

The function `requestRedemption` checks if the value to be redeemed is less than `minimumRedeemAmount` and would revert if it is less than the minimum value. This value can be set using `setRedeemMinimum` without any check for the value.

In case of malicious owner, or the account is compromised, they can set `minimumRedeemAmount` to a very large amount and no user would be able to redeem their collateral.

## [QA-03] pause and unpause roles should be given to the `PAUSER_ADMIN` role.

The pause and unpause functions should be controlled from the `PAUSER_ADMIN` role. In `CashManager` the pause role is correctly controlled by the `PAUSER_ADMIN` but the unpause role is controlled using the `MANAGER_ADMIN` role.

### POC

```solidity
function pause() external onlyRole(PAUSER_ADMIN) {
    _pause();
  }

  /**
   * @notice Will unpause minting functionality of this contract
   */
  function unpause() external onlyRole(MANAGER_ADMIN) {
    _unpause();
  }
```

### Recommendation

```solidity
function pause() external onlyRole(PAUSER_ADMIN) {
    _pause();
  }

  /**
   * @notice Will unpause minting functionality of this contract
   */
  function unpause() external onlyRole(PAUSER_ADMIN) {
    _unpause();
  }
```