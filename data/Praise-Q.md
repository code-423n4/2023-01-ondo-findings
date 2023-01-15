function _processRedemption() in CashManager.sol lacks the 'unchecked' keyword. 
https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L743-L770

One reason you might use unchecked is when looping through the elements of an array. Consider for example:
```
uint256 length = array.length;
for(uint256 i = 0; i < length; i++) {
  doSomething(array[i]);
}
```
Each time i++ is called under/overflow checks are made. But we're already constraining i by length, i < length, making those under/overflow checks unnecessary. So, we can rewrite the loop like this and potentially save significant gas:
```
uint256 length = array.length;
for(uint256 i = 0; i < length;) {
  doSomething(array[i]);
  unchecked{ i++; }
}
```