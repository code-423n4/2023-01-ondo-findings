1) Missing non-zero validation

Currently, a lot of contracts are missing a non-zero validation like in the following example:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L106

Consider adding a non-zero validation in order to reduce the risk of DoS for several functions.

In order to keep the contract size reasonable, I simply raised this issue once.

2) Guardian is internal

Currently, the guardian variable is internal. This might make it hard for average users to retrieve this important variable

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L48 

Consider marking it as public.

*The same issue applies to all factories.

3) `JumpRateModelV2` uses an outdated solidity version

Contrary to Compounds `JumpRateBaseModelV2`, the aforementioned contract uses an older solidity version with safemath.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L1

Consider updating the solidity version to >= 0.8.0 and remove safemath.

4) `JumpRateModelV2` uses magic numbers instead of constants

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L116

Consider implementing a constant variable and use this instead of magic numbers.

5) Types can get casted directly in the constructor parameters

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L128

Consider casting the corresponding types to variables directly in the constructor

6) Checks-effects-interactions pattern is not respected

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L215

Currently, the external call is executed before the increase in `mintRequestsPerEpoch`. While this doesn't lead to any issues at this point, it is still considered best practice to adhere to CEI. Please note that in terms of the before-after pattern (transfer-tax compatibility) the checks-effects-interactions pattern will be violated.

7) `setMintFee` has no reasonable limits

The `setMintFee` function allows the `MANAGER_ADMIN` to set an arbitrary `mintFee` which can result in an almost total loss of funds if setted to `9999`. Therefore we suggest a reasonable upper limit for this function. A further escalation point is when a user calls `claimMint` and the `MANAGER_ADMIN` frontruns the function call, setting `mintLimit` to `9999`. However, this is just rated as low since centralization risks are out of scope and we do not expect such behaviour from a trustworthy team like the Flux team. Anyways, an upper bound should still be added.

8) The `_processRefund` function doesn't decrease `redemptionInfoPerEpoch[epoch].totalBurned`

Currently, the `_processRefund` function resets `redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee]`. However, it doesnt decrease `redemptionInfoPerEpoch[epoch].totalBurned`.

While this issue doesn't expose a security threat, it might still become a future issue if another smart contract relies on this variable. Due to that fact, one could even raise this as medium issue. However, I will leave this decision by the judge.

Consider decreasing it for each loop step accordingly.

9) `setMintLimit` should at least validate `mintLimit >= currentMintAmount`

If thats not the case, the `requestMintFunction` will underflow without an explicit revert string.

Consider validating `mintLimit` to be at least `currentMintAmount`. Moreover, if that is ever the case it should be ensured this setting does not get frontrunned, otherwise the same issue will appear.