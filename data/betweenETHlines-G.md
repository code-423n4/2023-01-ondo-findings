1) Require statement can be checked before `_beforeTokenTransfer` 

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L36


To ensure early reverts, the require statement should be done before `_beforeTokenTransfer`. This will save some gas if the requirement fails because it doesnt execute `_beforeTokenTransfer`.

2) Require statements can be checked before `_beforeTokenTransfer`

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L63

Similar as in above issue, the checks can be done before the interaction which saves gas in revert cases.

*The same issue applies to `CashKycSenderReceiver` as well.

3) [GLOBAL] Emit events before variable is changed

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L83

Currently, in almost all contracts, events are emitted after the variables were changed. This results in a need to cache the old variables into memory. In order to save some gas, it might make sense to emit these events before the old variable was changed to remove the memory caching.

We will simply raise this issue once, in order to keep the report size reasonable

4) The `deployCash` function can simply deploy proxies and link them to the desired implementation

If the desired implementation is always the same and the factory is meant to deploy multiple contracts, it might make sense to create an additional function which only deploys the proxy and links it to the already deployed implementation instead of always deploying the whole bunch.
