## [G-01] Make function external instead of public if possible

### Description

Version 0.6.9 removed the restriction on public functions accepting calldata arguments. Public functions for Solidity versions 0.6.9 have to transfer the parameters to memory.

Note: valid only for solidity versions `<0.6.9`

### Findings

- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L530](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L530) =>   `function borrowBalanceStored(address account) public view returns (uint256);` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L532](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L532) =>   `function exchangeRateCurrent() public returns (uint256);` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L534](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L534) =>   `function exchangeRateStored() public view returns (uint256);` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L538](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L538) =>   `function accrueInterest() public returns (uint256);` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L637](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L637) =>   `function _becomeImplementation(bytes memory data) public;` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L642](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L642) =>   `function _resignImplementation() public;` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1013](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1013) =>   `function borrowBalanceStored(address account) public view returns (uint256) {` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1024](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1024) =>   `function exchangeRateCurrent() public returns (uint256) {` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1036](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1036) =>   `function exchangeRateStored() public view returns (uint256) {` || `pragma solidity ^0.5.12;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1059](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1059) =>   `function accrueInterest() public returns (uint256) {` || `pragma solidity ^0.5.12;`

### Resources

- [G009 - Make Function external instead of public](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g009---make-function-external-instead-of-public)
- [Public vs External Functions in Solidity | Gustavo (Gus) Guimaraes post](https://gus-tavo-guim.medium.com/public-vs-external-functions-in-solidity-b46bcf0ba3ac)
- [StackOverflow answer](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices?answertab=active#tab-top)

## [G-02] Use "require" instead of "assert" when possible

If `assert()` returns a false assertion, it compiles to the invalid opcode `0xfe`, which eats up all the gas left and completely undoes the changes. `require()` compiles to `0xfd`, which is the opcode for a `REVERT`, indicating that it will return the remaining gas if it returns a false assertion.

### Findings

- [contracts/cash/factory/CashFactory.sol#L97](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97) => `assert(cashProxyAdmin.owner() == guardian);`
- [contracts/cash/factory/CashKYCSenderFactory.sol#L106](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106) => `assert(cashKYCSenderProxyAdmin.owner() == guardian);`
- [contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106) => `assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);`

### References

- [Assert() vs Require() in Solidity – Key Difference & What to Use](https://codedamn.com/news/solidity/assert-vs-require-in-solidity)