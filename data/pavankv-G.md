## 1.  Use immutable instead of constant for keccak256 operation :-
The use of constant keccak variables results in extra hashing (and so gas). This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas. You should use immutables until the referenced issues are implemented, then you only pay the gas costs for the computation at deploy time.

code snippet:-
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26

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
