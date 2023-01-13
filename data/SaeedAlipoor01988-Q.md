In the below function 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L305

setter admin will set a new exchange rate for a certain epoch. But as you see, there is an if block, if rateDifference is more than maxDifferenceThisEpoch , we will set the exchange rate for the match epoch and call the pause function and emit the MintExchangeRateCheckFailed event. 

Mint Exchange Rate Check Failed! else we will AGAIN set the exchange rate for the match epoch and emit the MintExchangeRateSet event. Why are we changing the exchange rate for the match epoch in both scenarios? 

I think in first if block we should not make a change and we should remove 
epochToExchangeRate[epochToSet] = exchangeRate;

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

In below function
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L453

we are updating the recipient’s address to get the fee, but we don’t check whether this address is a zero address or not.
The same is happening for the below function

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L466
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L807

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

There is not access limit for the below function
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L576
it’s not the problem?

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

In the below code, can we use <= 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L672

this is the comparison between the requested amount and minimumRedeemAmount. because this will save gas and I think there is no problem. 

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

In the below function,
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L241
user comes and will claim for airdrop for a certain epoch, it’s not necessary to wait for the epoch to get end?

