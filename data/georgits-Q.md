## Use latest Solidity version with a stable pragma statement
Files - [CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol), [CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol), [CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol), [CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol), [JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol), [CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol), [CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol), [CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol), [cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol)

## Wrong input validation
CTokenCash.sol - [585](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L585)

CTokenModified.sol - [585](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L585)

```
 *@param redeemTokensIn The number of cTokens to redeem into underlying (only one of redeemTokensIn or redeemAmountIn may be non-zero)
 *@param redeemAmountIn The number of underlying tokens to receive from redeeming cTokens (only one of redeemTokensIn or redeemAmountIn may be non-zero)
```
Should be something like `redeemTokensIn != 0 && redeemAmountIn == 0 || redeemTokensIn == 0 && redeemAmountIn != 0` since only of them one can have non-zero value

## `initialize` can be called by anyone
CashKYCSender.sol  - [46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L46)

CashKYCSenderReceiver.sol - [46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L46)

## Should be `>` instead of `>=`
CashManager.sol - [413](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L413)

## Missing zero address checks
CashManager.sol - [452](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L452), [465](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L465)