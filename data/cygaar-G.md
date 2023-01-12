#1: A second call to `_getKYCStatus` can be avoided if `from == _msgSender()` because this value is already checked on line 64. https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L68

#2: Similarly in CashKYCSenderReceiver.sol, a second call to `_getKYCStatus` can be avoided if `from == _msgSender()` or `to == _msgSender()` here: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L68 and here: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L76. 

#3: The two for-loops in KYCRegistry.sol can place the iterator in `unchecked` blocks to save a bit of gas. These loops are bounded and therefore won't overflow.

#4: `epochToExchangeRate[epoch]` is read twice in `claimMint`: [here](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L249) and [here](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L492). To reduce the extra `SLOAD`, you can move the 0 check to `_getMintAmountForEpoch ()` so you only need to read the value once.

#5: In `completeRedemptions`, the input `collateralAmountToDist` can be provided less the `fees` directly so this [line](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L723) isn't needed.

#6: The for-loops in `CashManager.sol` can also move their iterators into `unchecked` blocks. Because these loops are restricted, the iterator will not overflow.