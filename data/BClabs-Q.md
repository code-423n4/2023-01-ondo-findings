
1. Zero checks are missing
Zero checks are missing when setting fee and asset receipient.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L465-L471
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L452-L458

2. If rateDifference is higher than maxDifferenceThisEpoch, the contract will pause, which is fine. However when contract is unpaused everytime the setMintExchangeRate will be
called, the contract will get paused again, because lastSetMintExchangeRate is not updated during the pausing. So overrideExchangeRate would have to be called every time contact unpauses, to prevent this. To fix this set lastSetMintExchangeRate = exchangeRate.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L281-L319

3. Should check that setFeeRecipient and setAssetRecipient are not called with address(0) since ERC20 does not allow transfers to address(0), 
meaning all requestMints would get rejected.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L452-L454
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L465-L467

4. Contracts should use the same solidity version, otherwise there can be some collisions. Another point would be to use static solidity versions instead of floating.
JumpRateModelV2 uses ^0.5.16
OndoPriceOracle uses 0.6.12
OndoPriceOracleV2 uses 0.8.16
CTokenDelegate uses ^0.8.10

5. Duplicated content in IOndoPriceOracle and IOndoPriceOracleV2
Both files containt interface IOndoPriceOracle. One implementation would be enough.

6. Users can not redeem their cash for collateral unless admin includes them in the redeemer's array as a parameter in completeRedemptions function. This mean that in case admin's database gets corrupted, he can lose track of which users should be included as redeemers in some epoch. This is a big centralisation problem, since user's tokens can get locked in the protocol indefinitely in case admin doesn't include them. To fix it, give power to complete redemption to the user.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L707-L713
