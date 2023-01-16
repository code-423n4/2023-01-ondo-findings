**contracts/cash/token/Cash.sol**
- L38 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/tokens/cCash/CCashDelegate.sol**
- L32/47 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/tokens/cToken/CTokenDelegate.sol**
- L32/47 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/token/CashKYCSender.sol**
- L65/72 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/OndoPriceOracle.sol**
- L69/70/81/83/96/98/107/109 - A variable is created in memory that is only used once, it can be used directly where the operation is needed and not create an unnecessary variable.

- L122 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/token/CashKYCSenderReceiver.sol**
- L65/72/80 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/factory/CashFactory.sol**
- L152 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/factory/CashKYCSenderFactory.sol**
- L165 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/factory/CashKYCSenderReceiverFactory.sol**
- L165 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/JumpRateModelV2.sol**
- L89 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/kyc/KYCRegistry.sol**
- L87/90 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/tokens/cCash/CCash.sol**
- L153/157/271 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/tokens/cToken/CErc20.sol**
- L153/157/271 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L159/160/180/181/223/224/240/241 - A variable is created in memory that is only used once, therefore gas could be saved if the operation is directly used within where it is needed.


**contracts/lending/OndoPriceOracleV2.sol**
- L217/282 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/cash/CashManager.sol **
- L491/492 - A variable is created in memory that is only used once, therefore gas could be saved if the operation is directly used within where it is needed.


**contracts/lending/tokens/cErc20ModifiedDelegator.sol**
- L727/1253 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/lending/tokens/cCash/CTokenCash.sol**
- L44/47/54/67/586/933/1357/1389 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L53 - It is less expensive to validate the input variable than the variable in storage.

- L330/331 - A variable is created in memory that is only used once, therefore you could save that creation of the variable and directly use the operation where it is needed.

- L370/371/374/377 - Instead of creating so many variables in memory, you could directly return
return ((getCashPrior() + totalBorrows - totalReserves) * expScale) / _totalSupply;

- L74/1436/1438 - Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.
These are new cases of something that we found in the automated findings.


**contracts/lending/tokens/cToken/CTokenModified.sol**
- L44/47/54/67/586/933/1360/1392 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L53 - It is less expensive to validate the input variable than the variable in storage.

- L330/331 - A variable is created in memory that is only used once, therefore you could save that creation of the variable and directly use the operation where it is needed.

- L370/371/374/377 - Instead of creating so many variables in memory, you could directly return
return ((getCashPrior() + totalBorrows - totalReserves) * expScale) / _totalSupply;

- L74/1439/1441 - Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.
These are new cases of something that we found in the automated findings.
