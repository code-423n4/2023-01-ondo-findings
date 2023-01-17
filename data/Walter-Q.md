1)  https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/factory/CashFactory.sol#L54
need to check if guardian is different from address(0) ,require (guardian != address(0))revert..
2) https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L546-L552
is it better to call "updateEpoch" when we'll update the EpochDuration,in this way the epochs will be as little messed up as possible

3) https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L110
In my opinion it is more able to satisfy the comment above, a mapping of all epochs with relative mintLimits,like this:
mapping(uint256 => mapping(address => uint256)) public mintLimitperEpoch;