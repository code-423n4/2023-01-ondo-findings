1.  Cash factory contract deployment count could be reduces

We could save gas by not deploying cash contract and proxyAdmin for every proxy, but could use the same implementations for all the proxies. Each contract deployment costs at least 53,000gas for the create + transaction costs alone (without implementation). This means contract deployments should be minimised every time that's possible.

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L79-L85