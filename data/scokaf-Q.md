# Number 1: Floating Pragma

Vulnerability details

## Impact

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.10, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

For reference, see https://swcregistry.io/docs/SWC-103

## Proof of Concept

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L1

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCash.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CErc20.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L2

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cToken/CTokenModified.sol#L2



## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Remove ^ in “pragma solidity ^0.8.10” and change it to “pragma solidity 0.8.10” to be consistent with the rest of the contracts.


# Number 2: More Readable Constants


Vulnerability details

## Impact

Some constant values are difficult to read at one time because they have a lot of 0's.
Solidity allows _ to separate series of zero's

## Proof of Concept

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/JumpRateModelV2.sol#L29

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L77 



## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Replace 2628000 with 2_628_000
Replace 90000 with 90_000

# Number 3: Imports placed before pragma directive

Vulnerability details

## Impact

It is a norm to declare the pragma version for example pragma solidity 0.8.17; before adding imports used in a solidity smart contract. In a list of contracts, the imports are added before the pragma solidity version. For ease of readability and as a norm, the pragma directive should be added before the imports in a solidity smart contract.

## Proof of Concept

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L17  

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L17 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L18 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L17 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L18 


## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Add pragma solidity version before adding imports in contract e.g:

pragma solidity 0.8.16;

import "contracts/cash/kyc/KYCRegistryClient.sol";


# Number 4: Use of block.timestamp 

Vulnerability details

## Impact

Some contracts use block.timestamp which can be problematic as Miners can alter block.timestamp with the following restrictions.

It cannot bear a time stamp that is earlier than that of its parent.
It won't be too long from now.

## Proof of Concept

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistry.sol#L92 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L294 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L174 

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L584 



## Tools Used

Manual Analysis

## Recommended Mitigation Steps

1. Don't use block.timestamp for a source of entropy and random number
2. Use of trusted oracles

# Number 5: Use of DelegateCall

Vulnerability details

## Impact

Delegatecall is difficult to utilize, and its application or misinterpretation might have disastrous effects.

Two considerations are necessary while utilizing delegatecall.

context is preserved via delegatecall (storage, caller, etc...)
The contract calling delegatecall and the contract being called must have the same storage configuration.



## Proof of Concept

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/interfaces/IMulticall.sol#L26  

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/OndoPriceOracleV2.sol#L66 



## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Use stateless Library


