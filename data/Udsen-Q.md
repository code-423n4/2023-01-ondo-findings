## 1. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

Currently the **TransparentUpgradeableProxy** is imported as follows:

	import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

But it can be imported explicitly by the name as follows:

	import {TransparentUpgradeableProxy} from "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L18

There are 17 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L18
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L4
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L4
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L18-L19
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L17-L18
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L18-L19
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L19-L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L19-L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L19-L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L3-L4
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L18-L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L4
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L4
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L17-L19
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L4-L7
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L4-L7
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L17-L26

## 2. NATSPEC IS MISSING

NatSpec is missing for the following functions , constructor and modifier

	contract TokenProxy is TransparentUpgradeableProxy {
		constructor(
			address _logic,
			address _admin,
			bytes memory _data
		) TransparentUpgradeableProxy(_logic, _admin, _data) {}
	}
	
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L21
	
There are 1 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L29

## 3. NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

	pragma solidity ^0.8.10;
	
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

There are 6 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L1
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L2
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L2
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2

## 4. MISSING `address(0)` CHECKS FOR THE FOLLOWING ADDRESS INPUTS TO THE CONSTRUCTORS AND FUNCTIONS.

    _setFTokenToCToken(fToken, cToken);
	
By mistake fToken and cToken could be set to address(0). It is a good practice to check for address(0).	

	require(fToken != address(0), "Address cannot be zero");
	require(cToken != address(0), "Address cannot be zero");
	
Hence it is recommended to check for the address(0) for the fToken adn cToken variable before ` _setFTokenToCToken(fToken, cToken)` is called.
	
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L92-L99

There are 9 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L66
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L157
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L161
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L455
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L469
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L108
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L43
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L183
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L807

## 5.`assert()` FUNCTION IS USED WITHOUT DESCRIPTION MESSAGE FOR THE REVERT.

    assert(cashProxyAdmin.owner() == guardian);

Add a description message to the assert function for the revert. It is very important to add a revert message, then `msg.sender` has enough information to learn the reason of failure.

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97

There are 2 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106

## 6. USE NAMED RETURN VALUES FOR BETTER READABILITY AND UNDERSTANDING OF THE CODE

	) external payable override onlyGuardian returns (bytes[] memory results) { 
		results = new bytes[](exCallData.length);
		
above code can be changed as follows for better readability and understanding

	) external payable override onlyGuardian returns (bytes[] memory) { 
		bytes[] memory results = new bytes[](exCallData.length);
		...
		...
		return results;
		
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L123-L134

There are 2 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L133-L144
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L133-L144

## 7. PROPER COMMENTING WILL INCREASE THE READABILITY AND UNDERSTANDING OF THE CODE.

    // `_logic' is set to `address(cash)`.
 
Above comment should be changed as **`_logic' is set to `address(CashKYCSender)`**
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L34
 
    // Following the above mentioned deployment, the address of the CashFactory contract will: 
 
Above comment should be changes as **Following the above mentioned deployment, the address of the CashKYCSenderFactory contract will:**
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L36 

    // `_logic' is set to `address(cash)`.
 
Above comment should be changed as **`_logic' is set to `address(CashKYCSenderReceiver)`**
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L34

    // Following the above mentioned deployment, the address of the CashFactory contract will: 
 
Above comment should be changes as **Following the above mentioned deployment, the address of the CashKYCSenderReceiver contract will:**
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L36

    // @param repayAmount The amount to repay, or -1 for the full outstanding amount 

Above comment says **-1** can be given as an input. But repayAmount is declared as uint and can not accept negative values. Should change the comment accordingly.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L107
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L118
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L107
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L118

    // @param addAmount The amount fo underlying token to add as reserves
	
The **fo** of above comment should be changed to **of**
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L165
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L165

    // @param chainlinkOracle cToken contract address

In the above comment the parameter represents **chainlink oracle address** and not the cToken address.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L249

	// Mapping from address to amount of CASH address burned
	
Above comment should be changed as follows:
	// Mapping from address to amount of CASH amount burned
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L74

	// 1) The total supply at the end of the epoch is stored 
	
The condition required in the above comment is not implemented inside the function.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L570-L587

## 8. CONSTANTS SHOULD BE DECLARED IN UPPERCASE LETTERS FOR BETTER READABILITY OF THE CODE.

    uint public constant blocksPerYear = 2628000; 
  
Above constant should be declared as:

    uint public constant BLOCKS_PER_YEAR = 2628000; 	
  
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29

There are 10 more instances of this issue

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L38
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L166
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L184
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L113
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L164
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L182


## 9. USE DATA TYPES WITH EXPLICIT DATA SIZE, WHEN DECLARING VARIABLES FOR BETTER READABILITY OF THE CODE.

    uint public multiplierPerBlock;
  
Above variable can be declared as `uint256` as follows:

    uint256 public multiplierPerBlock;
  
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L34

There are multiple occurences of this issue in the code base.

## 10. USE `SafeMathLib.sol` FOR SECURED MATH OPERATIONS INVOLVING `uint`.

      uint256 collateralAmountDue = (amountToDist * cashAmountReturned) / 
        quantityBurned;
		
The arithmetic functions of the `SafeMathLib.sol` can be used instead.
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L755-L756

## 11. CHECK CONTRACT EXISTENCE WHEN USING LOW LEVEL `call` FUNCTION TO SEND ETHER.

      (bool success, bytes memory ret) = address(exCallData[i].target).call{
        value: exCallData[i].value
      }(exCallData[i].data);
	  
Low level `call` function will return `true` even if the destination contract does not exist.
Hence use `EXTCODESIZE` low level command to check the contract existence at the destination address before transfering **ETH**

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L962-L964