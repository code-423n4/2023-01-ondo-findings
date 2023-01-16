1.
Title: empty `constructor`

Proof of Concept:
[CCashDelegate.sol#L15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L15)
[CTokenDelegate.sol#L15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L15)

Recommended Mitigation Steps:
Remove if unused for gas saving
________________________________________________________________________

2.
Title: Expression for `constant` values such as a call to `keccak256()`, should use `immutable` rather than `constant`

Proof of Concept:
[Cash.sol#L22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22)
[CashKYCSender.sol#L26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26)
[CashKYCSenderReceiver.sol#L26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L26)

Recommended Mitigation Steps:
Change from `constant` to `immutable`
reference: [here](https://github.com/ethereum/solidity/issues/9232)
________________________________________________________________________

3.
Title: function getUnderlyingPrice() gas improvement on returning value

Proof of Concept:
[OndoPriceOracleV2.sol#L95](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L95)

Recommended Mitigation Steps:
by set `prices` in returns L#94 and delete L#95 can save gas

```
) external view override returns (uint256 prices) { { //@audit-info: set here
```
________________________________________________________________________

4.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[OndoPriceOracleV2.sol#L292-L296](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292-L296)

Recommended Mitigation Steps:
Change to:

```
	 require((answeredInRound >= roundId),"Chainlink oracle price is stale");

         require((updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),"Chainlink oracle price is stale");
```
________________________________________________________________________