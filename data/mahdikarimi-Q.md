## Admin can set invalid epoch duration ( 0 ) 

set the epoch duration to zero make the epoch transition impossible until admin change it to a non-zero value 
If epochDuration is being set to zero , the following line in transitionEpoch function always reverts (because dividing by zero is not possible and reverts ) . 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L577-L578

consider add a zero-value check in setEpochDuration function to make sure reaching this state isn't possible . 

## mintAmount can be higher than mintLimit after setMintLimit during ongoing epoch 

It's possible that admin set a value for mintLimit that is lower than mintAmount and this isn't a valid state ( we know that mintAmount should not exceed mintLimit and may affect your calculations ) .
I recommend use newMintLimit for next epoch and not change mintLimit of an ongoing epoch also you can add a check to prevent this .

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L596-L600