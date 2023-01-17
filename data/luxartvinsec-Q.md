# 1. Insecure Initialization of the contract

Link: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L31

Summary: The CashKYCSender contract uses the _disableInitializers() function in its constructor, which disables the use of the initializer functions. This allows any address to call the initialize() function, potentially allowing them to set arbitrary values for the kycRegistry and kycRequirementGroup variables and bypassing the intended KYC checks.

Impact: An attacker can potentially call the initialize() function and set arbitrary values for the kycRegistry and kycRequirementGroup variables, potentially allowing them to bypass KYC checks and transfer tokens to unauthorized addresses.

Recommendation: The _disableInitializers() function should be removed or replaced with a secure initialization process that properly controls access to the initialize() function. Additionally, the role-based access control in the "onlyRole" function should be reviewed to ensure that it is configured correctly.

# 2. Insecure Internal Function Access in OndoPriceOracle Contract

Link : https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L119

Summary:
The OndoPriceOracle contract contains an internal function "_setFTokenToCToken()" that is called by the external function "setFTokenToCToken()". The "_setFTokenToCToken()" function does not have any access restrictions and can be called by any address, potentially allowing an attacker to associate arbitrary fTokens with arbitrary cTokens.

Impact:
An attacker could associate arbitrary fTokens with arbitrary cTokens, potentially manipulating the underlying prices of the fTokens, leading to financial losses for users and potentially destabilizing the lending market.

Recommendation:
The "_setFTokenToCToken()" function should have the same access restrictions as the "setFTokenToCToken()" function, only allowing the contract owner to call it. Additionally, proper input validation should be implemented to ensure that only valid fToken and cToken addresses are accepted by the function.

# 3. Missing access control check on _beforeTokenTransfer function

Link: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L56

Summary: The _beforeTokenTransfer function in the CashKYCSenderReceiver contract checks the KYC status of the sender, but does not check for the presence of a specific role before allowing the transfer. This could allow any address to transfer tokens, regardless of their KYC status.

Impact: This vulnerability could allow unauthorized addresses to transfer tokens from the contract, potentially leading to financial loss.

Recommendation: Add an access control check on the _beforeTokenTransfer function, such as using the onlyRole function from the OpenZeppelin library, to ensure that only authorized addresses are able to initiate token transfers. This would prevent unauthorized addresses from transferring tokens from the contract and mitigate the risk of financial loss.

# 4. Missing input validation in `CCash.mint()` function

Link: https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L66

Summary: 
The CCash contract's `mint()` function, which allows users to supply assets into the market and receive `cTokens` in exchange, does not properly validate user input. This could allow malicious actors to supply an arbitrary, potentially large amount of assets, potentially causing issues with the contract's operation or causing loss of funds.

Impact: A malicious actor could potentially supply an arbitrarily large amount of assets to the contract, potentially causing issues with the contract's operation or causing loss of funds.

Recommendation: Input validation should be added to the `mint()` function to ensure that only valid, expected amounts of assets are supplied to the contract. Example:
```
require(mintAmount > 0, "Mint amount should be greater than 0");
```
This will ensure that the mint amount is greater than 0, and if not the function will revert with the message "Mint amount should be greater than 0".
