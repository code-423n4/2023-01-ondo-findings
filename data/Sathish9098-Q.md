# LOW FINDINGS

##

## [L-1] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

[FILE : 2023-01-ondo/contracts/cash/Proxy.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol)

        import "contracts/cash/external/openzeppelin/contracts/proxy/TransparentUpgradeableProxy.sol";

[FILE: 2023-01-ondo/contracts/cash/token/Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)

        import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol)

       import "./CCash.sol";

[FILE : 2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol)

    import "./CErc20.sol";

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol)

     import "./IOndoPriceOracle.sol";
     import "contracts/lending/compound/Ownable.sol";

[FILE : 2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

       import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";
       import "contracts/cash/kyc/KYCRegistryClientInitializable.sol";

[FILE : 2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol)

       import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
       import "contracts/cash/Proxy.sol";
       import "contracts/cash/token/Cash.sol";
       import "contracts/cash/interfaces/IMulticall.sol";

[FILE : 2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol)

       import "contracts/cash/external/openzeppelin/contracts/proxy/ProxyAdmin.sol";
       import "contracts/cash/Proxy.sol";
       import "contracts/cash/token/CashKYCSender.sol";
       import "contracts/cash/interfaces/IMulticall.sol";

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)

      4: import "./CTokenCash.sol";

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol ](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol)

       import "contracts/lending/tokens/cErc20Delegate/ComptrollerInterface.sol";
       import "contracts/lending/tokens/cErc20Delegate/InterestRateModel.sol";
       import "contracts/lending/tokens/cErc20Delegate/EIP20NonStandardInterface.sol";
       import "contracts/lending/tokens/cErc20Delegate/ErrorReporter.sol";

##

## [ L-2]  MIXING AND OUTDATED COMPILER

The pragma version used are:

pragma solidity ^0.8.10;

Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.

The minimum required version must be 0.8.17; otherwise, contracts will be affected by the following important bug fixes:

0.8.14:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.
0.8.15

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.
0.8.16

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.
0.8.17

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.
Apart from these, there are several minor bug fixes and improvements

[FILE : 2023-01-ondo/contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol)

        1:  pragma solidity ^0.5.16;

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol)

           15 : pragma solidity 0.6.12;

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol)

           2 : pragma solidity ^0.8.10;


[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol)

         2:  pragma solidity ^0.8.10;

[FILE : 2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol)

       2:  pragma solidity ^0.8.10;

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol ](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol)

      2:  pragma solidity ^0.8.10;

[FILE: 2023-01-ondo/contracts/cash/token/Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)

       16:  pragma solidity 0.8.16;

##

## [L-3] DECIMALS() NOT PART OF ERC20 STANDARD

 decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

         decimalsMultiplier =
        10 **
        (IERC20Metadata(_cash).decimals() -
        IERC20Metadata(_collateral).decimals());
  
       

# NON-CRITICAL FINDINGS

##

## [NC-1] USE A MORE RECENT VERSION OF SOLIDITY

The latest solidity version is 0.8.17 . This fixes an important bug, makes overflow checks on multiplication more efficient and adds an LSP feature to always analyze all files in a project.

[FILE : 2023-01-ondo/contracts/lending/JumpRateModelV2.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol)

        1:  pragma solidity ^0.5.16;

[FILE : 2023-01-ondo/contracts/lending/OndoPriceOracle.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol)

         15:  pragma solidity 0.6.12;

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CCashDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol)

       2 :  pragma solidity ^0.8.10;

[FILE : 2023-01-ondo/contracts/lending/tokens/cToken/CTokenDelegate.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol)

       2:  pragma solidity ^0.8.10;

[FILE : 2023-01-ondo/contracts/cash/Proxy.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol)

       16:  pragma solidity 0.8.16;

[FILE: 2023-01-ondo/contracts/cash/token/Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)

       16:  pragma solidity 0.8.16;

[FILE : 2023-01-ondo/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol ](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol)

      2:  pragma solidity ^0.8.10;

##

## [NC-2] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

[FILE: 2023-01-ondo/contracts/cash/token/Cash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol)

      22:  bytes32 public constant TRANSFER_ROLE = keccak256("TRANSFER_ROLE");

[FILE : 2023-01-ondo/contracts/cash/token/CashKYCSender.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol)

    26 :   bytes32 public constant KYC_CONFIGURER_ROLE =
    keccak256("KYC_CONFIGURER_ROLE");

[FILE : 2023-01-ondo/contracts/cash/token/CashKYCSenderReceiver.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol)

       bytes32 public constant KYC_CONFIGURER_ROLE =
         keccak256("KYC_CONFIGURER_ROLE");

[FILE : 2023-01-ondo/contracts/cash/factory/CashFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol)

         44:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

         45:  bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

         46:   bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

[FILE : 2023-01-ondo/contracts/cash/factory/CashKYCSenderFactory.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol)

         44:  bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

         45:  bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");

         46:  bytes32 public constant DEFAULT_ADMIN_ROLE = bytes32(0);

[FILE : 2023-01-ondo/contracts/cash/kyc/KYCRegistry.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol)

           bytes32 public constant _APPROVAL_TYPEHASH =
           keccak256(
          "KYCApproval(uint256 kycRequirementGroup,address user,uint256 deadline)"
           );
 
           bytes32 public constant REGISTRY_ADMIN = keccak256("REGISTRY_ADMIN");

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

          122 :  bytes32 public constant MANAGER_ADMIN = keccak256("MANAGER_ADMIN");

          123:   bytes32 public constant PAUSER_ADMIN = keccak256("PAUSER_ADMIN");

          124:   bytes32 public constant SETTER_ADMIN = keccak256("SETTER_ADMIN");

##

## [NC-3]  Shorter inheritance list

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

       contract CashManager is
       ICashManager,
       IMulticall,
       AccessControlEnumerable,
      KYCRegistryClientConstructable,
      Pausable,
      ReentrancyGuard

##

## [NC-4]  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES (20)

##

## [NC-5]  TYPOS

[FILE : 2023-01-ondo/contracts/cash/CashManager.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol)

     /// @audit  BPS_DENOMINAOR   => BPS_DENOMINATOR

       429:  *      BPS_DENOMINAOR (say 9999) and `mintFee` = 1,

[FILE: 2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol)

         /// @audit  checkpointed =>  check pointed

       391:    * @dev This calculates interest accrued from the last checkpointed block

##

## [NC-6]  LINE WIDTH

Keep line width to max 120 characters for better readability.

[FILE: 2023-01-ondo/contracts/lending/tokens/cCash/CTokenCash.sol](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol)

   573:  * @param redeemTokensIn The number of cTokens to redeem into underlying (only one of redeemTokensIn or redeemAmountIn may be non-zero)
   574:  * @param redeemAmountIn The number of underlying tokens to receive from redeeming cTokens (only one of redeemTokensIn or redeemAmountIn may be non-zero)








    






NC-1	Missing checks for address(0) when assigning values to address state variables	6
NC-2	Return values of approve() not checked	1
NC-3	Event is missing indexed fields	74
NC-4	Constants should be defined rather than using magic numbers	1
NC-5	Functions not used internally could be marked external	8

L-1	Unsafe ERC20 operation(s)	6
M-1	Centralization Risk for trusted owners	41