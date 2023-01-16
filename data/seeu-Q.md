## [L-01] Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

- [contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol) => `pragma solidity ^0.5.16;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol) => `pragma solidity ^0.5.12;`

### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

## [L-02] SPDX-License-Identifier missing

### Description

Missing license agreements (SPDX-License-Identifier) may result in lawsuits and improper forms of use of code.

### Findings

- [contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol) => `The Solidity file is missing the SPDX-License-Identifier`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol) => `The Solidity file is missing the SPDX-License-Identifier`

### References

- [Missing license identifier | UMA DVM 2.0 Audit](https://blog.openzeppelin.com/uma-dvm-2-0-audit/)


## [L-03] Unnamed return parameters

### Description

To increase explicitness and readability, take into account introducing and utilizing named return parameters.

### Findings

```Solidity
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol
::66 =>   function mint(uint mintAmount) external override returns (uint) {
::77 =>   function redeem(uint redeemTokens) external override returns (uint) {
::100 =>   function borrow(uint borrowAmount) external override returns (uint) {
::110 =>   function repayBorrow(uint repayAmount) external override returns (uint) {
::168 =>   function _addReserves(uint addAmount) external override returns (uint) {
::179 =>   function getCashPrior() internal view virtual override returns (uint) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol
::66 =>   function mint(uint mintAmount) external override returns (uint) {
::77 =>   function redeem(uint redeemTokens) external override returns (uint) {
::100 =>   function borrow(uint borrowAmount) external override returns (uint) {
::110 =>   function repayBorrow(uint repayAmount) external override returns (uint) {
::168 =>   function _addReserves(uint addAmount) external override returns (uint) {
::179 =>   function getCashPrior() internal view virtual override returns (uint) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol
::210 =>   function balanceOf(address owner) external view override returns (uint256) {
::220 =>   function balanceOfUnderlying(address owner) external override returns (uint) {
::246 =>   function getBlockNumber() internal view virtual returns (uint) {
::254 =>   function borrowRatePerBlock() external view override returns (uint) {
::267 =>   function supplyRatePerBlock() external view override returns (uint) {
::281 =>   function totalBorrowsCurrent() external override nonReentrant returns (uint) {
::338 =>   function exchangeRateCurrent() public override nonReentrant returns (uint) {
::348 =>   function exchangeRateStored() public view override returns (uint) {
::357 =>   function exchangeRateStoredInternal() internal view virtual returns (uint) {
::385 =>   function getCash() external view override returns (uint) {
::394 =>   function accrueInterest() public virtual override returns (uint) {
::1085 =>   function _acceptAdmin() external override returns (uint) {
::1198 =>   function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
::1253 =>   function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
::1400 =>   function _getKYCStatus(address account) internal view returns (bool) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol
::210 =>   function balanceOf(address owner) external view override returns (uint256) {
::220 =>   function balanceOfUnderlying(address owner) external override returns (uint) {
::246 =>   function getBlockNumber() internal view virtual returns (uint) {
::254 =>   function borrowRatePerBlock() external view override returns (uint) {
::267 =>   function supplyRatePerBlock() external view override returns (uint) {
::281 =>   function totalBorrowsCurrent() external override nonReentrant returns (uint) {
::338 =>   function exchangeRateCurrent() public override nonReentrant returns (uint) {
::348 =>   function exchangeRateStored() public view override returns (uint) {
::357 =>   function exchangeRateStoredInternal() internal view virtual returns (uint) {
::385 =>   function getCash() external view override returns (uint) {
::394 =>   function accrueInterest() public virtual override returns (uint) {
::1088 =>   function _acceptAdmin() external override returns (uint) {
::1201 =>   function _addReservesFresh(uint addAmount) internal returns (uint, uint) {
::1256 =>   function _reduceReservesFresh(uint reduceAmount) internal returns (uint) {
::1403 =>   function _getKYCStatus(address account) internal view returns (bool) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol
::514 =>   function _scaleUp(uint256 amount) private view returns (uint256) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol
::115 =>   function DOMAIN_SEPARATOR() external view returns (bytes32) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol
::65 =>   function _getKYCStatus(address account) internal view returns (bool) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol
::324 =>   function _min(uint256 a, uint256 b) internal pure returns (uint256) {

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol
::755 =>   function mint(uint256 mintAmount) external returns (uint256) {
::768 =>   function redeem(uint256 redeemTokens) external returns (uint256) {
::781 =>   function redeemUnderlying(uint256 redeemAmount) external returns (uint256) {
::793 =>   function borrow(uint256 borrowAmount) external returns (uint256) {
::805 =>   function repayBorrow(uint256 repayAmount) external returns (uint256) {
::862 =>   function transfer(address dst, uint256 amount) external returns (bool) {
::900 =>   function approve(address spender, uint256 amount) external returns (bool) {
::928 =>   function balanceOf(address owner) external view returns (uint256) {
::941 =>   function balanceOfUnderlying(address owner) external returns (uint256) {
::967 =>   function borrowRatePerBlock() external view returns (uint256) {
::978 =>   function supplyRatePerBlock() external view returns (uint256) {
::989 =>   function totalBorrowsCurrent() external returns (uint256) {
::1001 =>   function borrowBalanceCurrent(address account) external returns (uint256) {
::1013 =>   function borrowBalanceStored(address account) public view returns (uint256) {
::1024 =>   function exchangeRateCurrent() public returns (uint256) {
::1036 =>   function exchangeRateStored() public view returns (uint256) {
::1047 =>   function getCash() external view returns (uint256) {
::1059 =>   function accrueInterest() public returns (uint256) {
::1144 =>   function _acceptAdmin() external returns (uint256) {
::1156 =>   function _addReserves(uint256 addAmount) external returns (uint256) {
::1168 =>   function _reduceReserves(uint256 reduceAmount) external returns (uint256) {
```

### References

- [Unnamed return parameters | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#unnamed-return-parameters)