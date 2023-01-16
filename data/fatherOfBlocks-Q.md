**contracts/lending/tokens/cCash/CCashDelegate.sol**
- L11 - An inheritance of CDelegateInterface is performed, but the import cannot be found.


**contracts/lending/tokens/cToken/CTokenDelegate.sol**
- L11 - An inheritance of CDelegateInterface is performed, but the import cannot be found.


**contracts/cash/factory/CashFactory.sol**
- L97 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**contracts/cash/factory/CashKYCSenderFactory.sol**
- L106 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**contracts/cash/factory/CashKYCSenderReceiverFactory.sol**
- L106 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**contracts/lending/tokens/cCash/CCash.sol**
- L6 - The CompLike interface is created, but it does not comply with the interface standards, a correct name could be ICompLike


**contracts/lending/tokens/cToken/CErc20.sol**
- L6 - The CompLike interface is created, but it does not comply with the interface standards, a correct name could be ICompLike

- L16 - CErc20Interface is used, but the import is not performed anywhere.


**contracts/lending/OndoPriceOracleV2.sol**
- L22/28 - The CTokenOracle and CTokenLike interfaces are created, but they do not comply with the interface standards, a correct name could be ICTokenOracle and ICTokenLike.


**contracts/lending/tokens/cErc20ModifiedDelegator.sol**
- L9/144 - The ComptrollerInterface and InterestRateModel interfaces are created, but they do not comply with the interface standards, a correct name could be IComptrollerInterface and IInterestRateModel

- L7/138/181/296/302/647 - Multiple old versions of solidity are used, this is something that should not be done if possible and the correct thing would be to use the latest version that is possible. https://docs.soliditylang.org/en/v0.8.17/installing-solidity.html


**contracts/cash/kyc/KYCRegistryClientConstructable.sol**
- L17/21 - The same class is imported twice and one of them is above the pragma, something that should not happen.


**contracts/lending/tokens/cCash/CTokenCash.sol**
- L6 - EIP20Interface is imported but never used, so it should be removed.

- L542/543/544 - There is commented code that is not used, therefore the comments are also incorrect.


**contracts/lending/tokens/cToken/CTokenModified.sol**
- L6 - EIP20Interface is imported but never used, so it should be removed.

- L542/543/544 - There is commented code that is not used, therefore the comments are also incorrect.


**contracts/lending/IOndoPriceOracle.sol**
- L18 - The PriceOracle interface is created and as a matter of standard it should be called IPriceOracle.


**contracts/lending/IOndoPriceOracleV2.sol**
- L18 - The PriceOracle interface is created and as a matter of standard it should be called IPriceOracle.

