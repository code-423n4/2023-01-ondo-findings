If we are adding message strings to require statements, we can reduce the gas by limiting the string length to 32 bytes.<br>

[Cash.sol#38](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#38)
[CCashDelegate.sol#30](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#30)
[CTokenDelegate.sol#32](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#32)
[CTokenDelegate.sol#47](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#47)
[CashKYCSender.sol#65](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#65)
[CashKYCSender.sol#72](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#72)
[OndoPriceOracle.sol#122](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#122)


