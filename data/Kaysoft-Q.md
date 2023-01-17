## [NC-01] Use named imports

Files: All files.

Consider using named imports like below

```jsx
import {CTokenCash} from "./CTokenCash.sol";

```

instead of 

```jsx
import "./CTokenCash.sol";

```

## [NC-02] Include the parameters in NatSpec comments
- [KYCRegistryClientInitializable.sol#L58](https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L58)

```jsx
/**
   * @dev Internal function to future-proof parent linearization. Matches OZ
   *      upgradeable suggestions
   */
  function __KYCRegistryClientInitializable_init_unchained(
    address _kycRegistry,
    uint256 _kycRequirementGroup
  ) internal onlyInitializing {
    _setKYCRegistry(_kycRegistry);
    _setKYCRequirementGroup(_kycRequirementGroup);
  }

```



## [L-01] Use latest Solidity stable version 0.8.17

Files: All files
Latest stable versions of Soildity compilers have bugfixes and security improvement.

see: https://swcregistry.io/docs/SWC-102
#### Recommended Mitigation Steps
Consider using latest Solidity version.


## [L-02] Avoid floating pragma
Files: All files

See: https://swcregistry.io/docs/SWC-103

#### Recommended Mitigation Steps
Consider using fixed pragma version like below

```jsx
pragma solidity 0.8.17;

```

## [L-03] Solidity compiler optimization can be problematic

```
const config: HardhatUserConfig = {
  solidity: {
    compilers: [
      {
        version: "0.8.16",
        settings: {
          optimizer: {
            enabled: true,
            runs: 100,
          },
        },
      },
```


### Description

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.


