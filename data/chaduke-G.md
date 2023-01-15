G1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L123
Putting it inside unchecked can save gas since overflow is impossible here:
```
uint dstTokensNew = accountTokens[dst] + tokens;

```

G2. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L182-L190
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

