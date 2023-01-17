# QA report

## FLOATING & OLD VERSION OF PRAGMA
 Use debugged complier version . Also use more recent compiler version. 
 
#### Affected Source Code
Total instances : 19
* [OndoPriceOracle.sol:15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L15)
* [JumpRateModelV2.sol:1](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L1)
* [IOndoPriceOracle.sol:15](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L15)
* [cErc20ModifiedDelegator.sol:7](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L7)
* [cErc20ModifiedDelegator.sol:138](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L138)
* [cErc20ModifiedDelegator.sol:138](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L138)
* [cErc20ModifiedDelegator.sol:181](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L181)
* [cErc20ModifiedDelegator.sol:296](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L296)
* [cErc20ModifiedDelegator.sol:302](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L302)
* [cErc20ModifiedDelegator.sol:324](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L324)
* [cErc20ModifiedDelegator.sol:647](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L647)
* [CTokenModified.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2)
* [CTokenInterfacesModified.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2)
* [CTokenDelegate.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2)
* [CErc20.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L2)
* [CTokenCash.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L2)
* [CCashDelegate.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2)
* [CCash.sol:2](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L2)
* [Comp.sol:1](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/compound/governance/Comp.sol#L1)


## LACK OF CHECKS ADDRESS(0)
The following methods have a lack of checks if the received argument is an address, it’s good practice in order to reduce human error to check that the address
specified in the constructor or initialize is different than address(0).

#### Affected Source Code
Total instances : 4
* [OndoPriceOracle.sol:80](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L80)
* [OndoPriceOracle.sol:106](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L106)
* [OndoPriceOracle.sol:119](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L119)
* [JumpRateModelV2.sol:64](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L64)

## LACK OF CHEKS FOR UNITS IN ORDER TO PREVENT SETTING DEFAULT VALUES. 
    In oder to prevent human errors its better to having a value for units rather than inizialize with 0 values.
    
Total instances : 2
* [OndoPriceOracle.sol:80](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L80)
* [JumpRateModelV2.sol:59](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L59-L63)

## Internal and private functions should have an underscore prefix with mixedCase(Naming convention)
#### Affected Source Code
Total instances : 7

* [JumpRateModelV2.sol:171](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L171)
* [cErc20ModifiedDelegator.sol:1200](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1200)
* [CTokenCash.sol:357](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L357)
* [CTokenCash.sol:491](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L491)
* [CTokenCash.sol:1427](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1427)
* [CCash.sol:179](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L179)
* [CCash.sol:193](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L193)

    For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#function-names)
    2. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)

    
 ## External & Public Functions should use mixedCase withot underscore
 #### Affected Source Code
 
 Total instances : 32
 
* [cErc20ModifiedDelegator.sol:548](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L548)
* [cErc20ModifiedDelegator.sol:324](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L324)
* [cErc20ModifiedDelegator.sol:548](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L548)
* [cErc20ModifiedDelegator.sol:552](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L552)
* [cErc20ModifiedDelegator.sol:554](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L554)
* [cErc20ModifiedDelegator.sol:558](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L558)
* [cErc20ModifiedDelegator.sol:562](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L562)
* [cErc20ModifiedDelegator.sol:564](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L564)
* [cErc20ModifiedDelegator.sol:602](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L602)
* [cErc20ModifiedDelegator.sol:624](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L624)
* [cErc20ModifiedDelegator.sol:637](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L637)
* [cErc20ModifiedDelegator.sol:642](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L642)
* [cErc20ModifiedDelegator.sol:720](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L720)
* [CTokenInterfacesModified.sol:360](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L360)
* [CTokenInterfacesModified.sol:364](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L364)
* [CTokenInterfacesModified.sol:366](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L366)
* [CTokenInterfacesModified.sol:370](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L370)
* [CTokenInterfacesModified.sol:374](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L374)
* [CTokenInterfacesModified.sol:376](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L376)
* [CTokenInterfacesModified.sol:416](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L416)
* [CTokenInterfacesModified.sol:438](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L438)
* [CTokenInterfacesModified.sol:451](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L451)
* [CTokenInterfacesModified.sol:456](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L456)
* [CTokenDelegate.sol:21](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L21)
* [CTokenDelegate.sol:39](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L39)
* [CErc20.sol:168](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L168)
* [CErc20.sol:236](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L236)
* [CErc20.sol:268](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L268)
* [CCashDelegate.sol:21](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L21)
* [CCashDelegate.sol:39](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L39)
* [CCash.sol:168](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L168)
* [CCash.sol:268](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L268)

  For more read...
    1. [Soliditydocs](https://docs.soliditylang.org/en/v0.8.15/style-guide.html#function-names)
 

##  Constants should be named with all capital letters with underscores separating words.(For Internal or private constants it should be started with underscore prefix)
#### Affected Source Code
Total instances : 7

* [JumpRateModelV2.sol:29](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29)
* [cErc20ModifiedDelegator.sol:208](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L208)
* [cErc20ModifiedDelegator.sol:213](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L213)
* [cErc20ModifiedDelegator.sol:350](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L350)
* [CTokenInterfacesModified.sol:33](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33)
* [CTokenInterfacesModified.sol:36](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36)
* [CTokenInterfacesModified.sol:113](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L113)

 For more read...
    1. [Solidity Style](https://www.notion.so/Solidity-Style-44daebebfbd645b0b9cbad7075ba42fe)


##  Remove assembly for future updates
its better not to use assembly because it reduce readability & future updatability of the code even though assembly reduce gas. 

Recommendation
Consider removeing all assembly code and re-implement them in Solidity to make the code significantly more clean.

Total instances : 7

* [cErc20ModifiedDelegator.sol:1205](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1205)
* [cErc20ModifiedDelegator.sol:1238](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1238)
* [cErc20ModifiedDelegator.sol:1259](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1259)
* [CErc20.sol:204](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L204)
* [CErc20.sol:244](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L244)
* [CCash.sol:204](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L204)
* [CCash.sol:244](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L244)

## incomplete comments

Total instances : 4
* [CTokenDelegate.sol:22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L22)
* [CTokenDelegate.sol:25](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L25)
* [CCashDelegate.sol:22](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L22)
* [CCashDelegate.sol:25](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L25)

## Unnecessary code(always false)
Total instances : 3

* [CTokenDelegate.sol:26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L26)
* [CCashDelegate.sol:26](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L26)
* [CCashDelegate.sol:41](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L41)

## Modifier and Events should top of the contract(Order of Layout)

* [CTokenCash.sol:1434](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1434)

For more read...
https://docs.soliditylang.org/en/v0.8.15/style-guide.html#order-of-layout