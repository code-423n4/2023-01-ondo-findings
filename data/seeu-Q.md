## Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

- [contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CErc20.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenInterfacesModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/tokens/cToken/CTokenModified.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol) => `pragma solidity ^0.8.10;`
- [contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol) => `pragma solidity ^0.5.16;`
- [contracts/lending/tokens/cErc20ModifiedDelegator.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol) => `pragma solidity ^0.5.12;`

### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)