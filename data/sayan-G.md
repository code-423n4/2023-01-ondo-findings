## 1. x = x + y is cheaper than x += y 
Affected Files:-
File: CashManager.sol line[582](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L582),[630](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L630)

## 2.Using `private` rather than `public` for constants, saves gas
Affected Files:-
File: CashManager.sol line [89](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L89),[122](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L122),[123](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L123),[124](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L124)
File: CashFactory.sol line : [44](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L44), [45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L45) ,[46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L46)
File: CashKYCSenderFactory.sol line: [44](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L44),[45](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L45),[46](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashKYCSenderFactory.sol#L46)
File: CashKYCSenderReceiverFactory.sol line: [44](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44),[45](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L45),[46](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L46)
File: CashKYCSenderSender.sol line: [26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26)
File: CashKYCSenderReciever.sol line:[26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L26)
