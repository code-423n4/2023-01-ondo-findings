## 1.  Use immutable instead of constant for keccak256 operation :-
The use of constant keccak variables results in extra hashing (and so gas). This results in the keccak operation being performed whenever the variable is used and called, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas. You should use immutables until the referenced issues are implemented, then you only pay the gas costs for the computation at deploy time.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L44
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L45
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L44
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L45
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L45
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L31
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L36
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L122
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L123
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L124

## 2.Ternary operation is cheaper than if-else statement
There are instances where a ternary operation can be used instead of if-else statement. In these cases, using ternary operation will save modest amounts of gas.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L64

Change from :-
 if (fTokenToUnderlyingPrice[fToken] != 0) {
      return fTokenToUnderlyingPrice[fToken];
    } else {
      // Price is not manually set, attempt to retrieve price from Compound's
      // oracle
      address cTokenAddress = fTokenToCToken[fToken];
      return cTokenOracle.getUnderlyingPrice(cTokenAddress);
    }
  }

TO:-
return (fTokenToUnderlyingPrice[fToken] != 0) ? fTokenToUnderlyingPrice[fToken] : cTokenOracle.getUnderlyingPrice(fTokenToCToken[fToken]) ;

## 3. Using Solidity version 0.8.17 will provide an overall gas optimization

Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value. Currently the contracts are compiled using version 0.8.7 (Foundry default). It is easily changeable to 0.8.17 using the command sed -i 's/0\.8\.7/^0.8.0/' test/*.sol && sed -i '4isolc = "0.8.17"' foundry.toml. This will have the following total savings obtained by forge snapshot --diff | tail -1:

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L16
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L16
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L15
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L16

And all filesin scope .

## 4. Increments/decrements can be unchecked in for-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L127
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L137
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L163
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L180
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L750
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L786
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L961

## 5. Multiple accesses of a mapping/array should use a local variable cache :-

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it
To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L39
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L97
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L95
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L86

## 6 . Use named returns for local variables where it is possible to save gas .

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L94

## 7.  Splitting require() statements that use && saves gas:-
&& opcode consume 3 gas so try to split the require statements to two require statement

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L293
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L46

## 8.Expressions that cannot be overflowed can be unchecked :-

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L297
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L299

## 9 . Use  x=x+y instead of x+=y for storage variables to save gas . 

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L630
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L649

## 10 . Checking msg.sender to not be zero address is redundant

There is an instance where msg.sender is checked not to be zero address. This check is redundant as no private key is known for this address, hence there can be no transactions coming from the zero address. The following diff removes this redundant check.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1087
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1090
