- `KYCRegistryClientConstructable` contains double import statements.
    ```solidity
    import "contracts/cash/kyc/KYCRegistryClient.sol";
    import "contracts/cash/kyc/KYCRegistryClient.sol";
    ```
- In CashManager contract more input validations should be added for privileged access restricted functions.
Like:
    - `setMintExchangeRateDeltaLimit` - `_exchangeRateDeltaLimit` must be less than `BPS_DENOMINATOR`. 
    - `setRedeemLimit` - `_redeemLimit` must not be less than `currentRedeemAmount`.

- Ownable - no getter function present for `pendingOwnerAddr` variable.
    ```solidity
    address private pendingOwnerAddr;
    ```
- `OndoPriceOracle.getUnderlyingPrice` should validate that `cTokenAddress != 0`. The the developer comments mentions a check but it is not implemented.
    ```solidity
      /**
       * @notice Retrieve the price of the provided fToken
       *         contract's underlying asset
       * @param fToken fToken contract address
       * @dev This function first attempts to check if the price has been set directly 
              in contract storage. If not set, we check if there is a corresponding cToken
       *      set within `fTokenToCToken` and piggy back on an external price oracle.
       */
      function getUnderlyingPrice(address fToken) external view override returns (uint256) {
        if (fTokenToUnderlyingPrice[fToken] != 0) {
          return fTokenToUnderlyingPrice[fToken];
        } else {
          address cTokenAddress = fTokenToCToken[fToken];   // @audit-issue - should validate cTokenAddress != 0, 
          return cTokenOracle.getUnderlyingPrice(cTokenAddress);
        }
      }
    ```
- During research and discussion it was mentioned by Ondo team that interactions with Compound's CEth contract could be possible in the future. However the current state of Ondo contracts are not compatible with CEth interactions. Eg: - the `OndoPriceOracleV2` contract calls `underlying()` and `decimals()` functions of Compound's CToken contracts, these invocations will get reverted in case of CEth.
