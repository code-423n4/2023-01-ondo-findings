**Multiple solidity versions**

0.6.12
0.8.16
0.8.10

Try to use one version of solidity 

Furthermore I implore the projects dev team to lock to one solidity version instead of floating(^) and 
let later version of solidity contracts interact with older versions
In my contact with the devs the stated reason for having pragma  0.6.12 version in PriceOracle.sol
Is that it interacts with CompoundLens.sol which is written in pragma 0.5.16. I implore the the dev team to try 
to upgraded the forked dependency contracts.

**Static  import**

In Cash.sol the import statement is:

import "contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20PresetMinterPauserUpgradeable.sol";

The dev team has most likely made som custom changes, the benefit of these custom changes my not out way ¨
the potensial risk of not following Openzeppelins guidelines, which are found here.

https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
 
