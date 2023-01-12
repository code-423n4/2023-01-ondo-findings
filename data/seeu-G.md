## Make function external instead of public if possible

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
