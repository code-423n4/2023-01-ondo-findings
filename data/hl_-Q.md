# Table of contents

[L-01] nonReentrant modifier should come immediately after visibility type
[L-02] Check missing in function setMintLimit() to ensure that mintLimit > 0
[L-03] Check missing in function setRedeemLimit() to ensure that redeemLimit > 0

## [L-01] nonReentrant modifier should come immediately after visibility type 

As best practice, nonReentrant modifier should come immediately after visibility type to conform to the Checks-Effects-Interactions pattern hence mitigating any possible re-entrancy risks. 

For example: 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L244

## [L-02] Check missing in function setMintLimit() to ensure that mintLimit > 0

Noted there is no check in function setMintLimit() to ensure that mintLimit > 0 per below code snippet: 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L596-L600

## [L-03] Check missing in function setRedeemLimit() to ensure that redeemLimit > 0

Noted there is no check in function setRedeemLimit() to ensure that redeemLimit > 0 per below code snippet: 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L609-L615