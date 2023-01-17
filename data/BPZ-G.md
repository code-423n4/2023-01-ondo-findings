## Gas Report

## ADD UNCHECKED {} FOR ITERATOR WHERE THE OPERANDS CANNOT OVERFLOW BECAUSE ITS ALLWAYS BELOW THE GIVEN NUMBER.
	
for loops j++ and i++ can be set to UNCHECKED{++j} and UNCHECKED{++i}

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L137

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L933

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961


## FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

if a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as
payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L805

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L819

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L855

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L898

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L909
