G1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L123
Putting it inside unchecked can save gas since overflow is impossible here:
```
uint dstTokensNew = accountTokens[dst] + tokens;

```

G2. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L182-L190
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L182-L190

No need to introduce ``src`` to save gas:
```
 function approve(
    address spender,
    uint256 amount
  ) external override returns (bool) {

    transferAllowances[msg.sender][spender] = amount;
    emit Approval(msg.sender, spender, amount);
    return true;
  }
```

G3. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L818-L819
Enclosing them inside unchecked can save gas since underflow is impossible due to previous checks
```
unchecked{
      uint accountBorrowsNew = accountBorrowsPrev - actualRepayAmount;
     uint totalBorrowsNew = totalBorrows - actualRepayAmount;
}

```

G4. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L1284
Encoding it inside unchecked can save gas - underflow is impossible due to previous check.
```
unchecked{
       totalReservesNew = totalReserves - reduceAmount;
}
```

G5. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L624-L626
This check should be performed earlier (at L588) to save gas.

G6. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L641
Enclosing this line into unchecked can save gas as underflow is impossible due to previous check.
```
unchecked{
totalSupply = totalSupply - redeemTokens;

}
```
G7. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L246-L248
Just using ``block.number`` directly without wrapping it as a function would save much gas since function call causes more gas.

G8. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1087
Dropping the second condition since the address(0) cannot initiate any transaction.

G9. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1102
Not reading the state variable ``pendingAdmin`` can save gas, we know it is address(0)
```
emit NewPendingAdmin(oldPendingAdmin, address(0));
```

G10. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1281
Enclosing this inside unchecked can save gas since underflow is impossible due to previous check.
```

totalReservesNew = totalReserves - reduceAmount;

```

G11.https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L1222
 Enclosing this inside unchecked can save gas since overflow is impossible here.
```
totalReservesNew = totalReserves + actualAddAmount;

```

G12. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L123
 Enclosing this inside unchecked can save gas since overflow is impossible here.
```
uint dstTokensNew = accountTokens[dst] + tokens;
```

G13. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L937-L940
No need to check this since seizeInternal() will revert with underflow when necessary. 

G14. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L296-L300
Enclosing this block with unchecked can save gas since underflow is impossible due to the condition used.
```
unchecked{
if (exchangeRate > lastSetMintExchangeRate) {
      rateDifference = exchangeRate - lastSetMintExchangeRate;
    } else if (exchangeRate < lastSetMintExchangeRate) {
      rateDifference = lastSetMintExchangeRate - exchangeRate;
    }
}

```

G15. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L864-L868
Use if-else rather than if-else-if can save gas here.
```
if (balance < previousBalance) {
      redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
    }
else{ 
      redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
    }
```

G16. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L721
Enclosing this inside unchecked can save gas since underflow is impossible.
```
unchecked{
   uint256 quantityBurned = redemptionInfoPerEpoch[epochToService]
      .totalBurned - refundedAmt;
}
```

