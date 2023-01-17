# [QA] - OpenZeppelin's library used is too old. An upgrade is recommended.

The OZ's contracts library used on `contracts/cash` is the version `v4.4.1`. However, the newest version available is `v4.7.0`. The recommendation is to upgrade the library and install it through forge as an external library instead of copy the files to an internal folder.

1. Install the library through forge command line:
```sh
  forge install Openzeppelin/openzeppelin-contracts --no-commit
  forge install Openzeppelin/openzeppelin-contracts-upgradeable --no-commit
```

2. Remove the files from `/contracts/cash/external/openzeppelin`

3. Add this line to your `remappings.txt` file:
```txt
  @openzeppelin/contracts/=lib/openzeppelin-contracts/contracts/
  @openzeppelin/contracts-upgradeable/=lib/openzeppelin-contracts-upgradeable/contracts/
```

4. Change all the references to the installed version. (Fix the breaking changes that could popup with this change)
```diff
- import "contracts/cash/external/openzeppelin/contracts/...";
+ import "@openzeppelin/contracts/...";

- import "contracts/cash/external/openzeppelin/contracts-upgradeable/access/IAccessControlEnumerableUpgradeable.sol";
+ import "@openzeppelin/contracts-upgradeable/access/IAccessControlEnumerableUpgradeable.sol";
```

# [QA] - Function params shadowing storage variables
If an input param has the same name than a storage variable, this one will be shadowed by that input variable. The recommendation is to change the names so these are unique.

## Where
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/Proxy.sol#L25 (`_admin` is shadowing a storage variable)


# [QA] - Different pragma versions across the project
It is recommended to use the same and fixed pragma version across the entire project. Currently we can find different versions on different contracts. 

For example:
* `/contracts/lending/tokens/cCash/CTokenCash.sol` is using `^0.8.10;`
* `/contracts/cash/Proxy.sol` uses `0.8.16`
* `/contracts/lending/OndoPriceOracle.sol` uses `0.6.12`

We recommend to change all the versions to `0.8.16` or `0.8.17` (last stable version).

# [QA] - Don't use `uint`. Use `uint256` instead.
It's a security risk to use `uint` (shorter version) over `uint256`. The mix between these two versions of the same variable type could be the source of bugs and unexpected behaviors because the signature of the function would be different even though the input types are virtually the same.

## Where
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/tokens/cCash/CTokenCash.sol#L37
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/tokens/cCash/CTokenCash.sol#L58

We recommend to use only the explicitly version `uint256` across the project.

# [QA] - Remove dead code and unused variables

## Where
https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/tokens/cCash/CCashDelegate.sol#L11-L50

```diff
  contract CCashDelegate is CCash, CDelegateInterface {
    /**
    * @notice Construct an empty delegate
    */
-   constructor() {}

    /**
    * @notice Called by the delegator on a delegate to initialize it for duty
-   * @param data The encoded bytes data for any initialization
    */
-   function _becomeImplementation(bytes memory data) public virtual override {
+   function _becomeImplementation(bytes calldata) public virtual override {
-     // Shh -- currently unused
-     data;

-     // Shh -- we don't ever want this hook to be marked pure
-     if (false) {
-       implementation = address(0);
-     }

      require(
        msg.sender == admin,
        "only the admin may call _becomeImplementation"
      );
    }

    /**
    * @notice Called by the delegator on a delegate to forfeit its responsibility
    */
    function _resignImplementation() public virtual override {
-     // Shh -- we don't ever want this hook to be marked pure
-     if (false) {
-       implementation = address(0);
-     }

      require(
        msg.sender == admin,
        "only the admin may call _resignImplementation"
      );
    }
  }
```

# [LOW] - pragma version older than 0.8 are being used on some contracts

It's not recommended to use solidity versions lower than `0.8` since this version has implemented security checks regarding integers overflows. It's highly recommended to update those contracts to a higher pragma version.

## Where
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/OndoPriceOracle.sol#L15
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/JumpRateModelV2.sol#L1
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/compound/Ownable.sol#L2

# [QA] - Floating pragma versions are recommended for interfaces
It's recommended to use floating pragma versions for interfaces

## Where
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/interfaces/IKYCRegistry.sol#L16
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/interfaces/IMulticall.sol#L16
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/interfaces/IKYCRegistryClient.sol#L16
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/IOndoPriceOracleV2.sol#L15
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/lending/IOndoPriceOracle.sol#L15
* https://github.com/code-423n4/2023-01-ondo/blob/12537cc94f4b0e9b213d53906aa2f95e425dd899/contracts/cash/interfaces/ICashManager.sol#L15

We recommend to use `^0.8.16` pragma version for those files.