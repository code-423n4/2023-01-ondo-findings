## 1. CONDUCT THE TOKEN TRANSFER AFTER THE REQUIRE CHECK, TO SAVE GAS INCASE OF REVERT.

Token transfer can be performed after all the require conditions related to transfer are succesful.
This will save gas in the event of transaction revert due to require condition not passing.

    super._beforeTokenTransfer(from, to, amount);

    require(
      _getKYCStatus(_msgSender()),
      "CashKYCSender: must be KYC'd to initiate transfer"
    );
	
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L61-L74

## 2. INCREMENTS/DECREMENTS CAN BE UNCHECKED IN FOR-LOOPS IF THEY DONOT UNDERFLOW OR OVERFLOW. 

    for (uint256 i = 0; i < length; i++) { 
	
This can be re-written as follows:

     for (uint256 i = 0; i < length;) {
      unchecked{ ++i;}
     }

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163

There is 1 more instance of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180

## 3. ARITHMATIC OPERATIONS CAN BE UNCHECKED IF PRIOR CONDITION CHECK PREVENTS IT FROM UNDERFLOW OR OVERFLOW.

    return balanceAfter - balanceBefore; 
	
Above substraction operation can be unchecked since `balanceAfter` is always greater than the `balanceBefore`.
Hence it can be re-written as:

    return unchecked{balanceAfter - balanceBefore;} 

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L224
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L224

## 4. REDUNDANT ASSIGNMENT OF VALUE CAN BE REMOVED TO SAVE GAS.

    currentEpoch = currentEpoch;

Above state variable assignment does not make any change to the code behavior and hence can be removed, to save gas during deployment.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L168