# Summary

| Id | Title |
| -- | ----- |
| 1 | No Storage Gap for Upgradeable Contracts |
| 2 | Admin manager can steal funds approved to CashManager |

# 1. No Storage Gap for Upgradeable Contracts

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/kyc/KYCRegistryClient.sol#L28

## Detail
For upgradeable contracts, there must be storage gap to "allow developers to freely add new state variables in the future without compromising the storage compatibility with existing deployments". Otherwise it may be very difficult to write new implementation code. Without storage gap, the variable in child contract might be overwritten by the upgraded base contract if new variables are added to the base contract. This could have unintended and very serious consequences to the child contracts.

Refer to the bottom part of this article: https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable

## Recommendation
Recommend adding appropriate storage gap at the end of upgradeable contracts such as the below. Please reference OpenZeppelin upgradeable contract templates.
```solidity
uint256[50] private __gap;
```


# 2. Admin manager can steal funds approved to CashManager

https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/cash/CashManager.sol#L962

## Detail
In CashManager contract, admin has a `multiexcall()` function, allowing them to call to arbitrary contract with any calldata. It can be abused to call to ERC20 token contract, and steal all funds that users approved to CashManager contract.
```solidity
function multiexcall(
  ExCallData[] calldata exCallData
)
  external
  payable
  override
  nonReentrant
  onlyRole(MANAGER_ADMIN)
  whenPaused
  returns (bytes[] memory results)
{
  results = new bytes[](exCallData.length);
  for (uint256 i = 0; i < exCallData.length; ++i) {
    (bool success, bytes memory ret) = address(exCallData[i].target).call{ 
      // @audit manager can steal funds approved to this contract of users
      value: exCallData[i].value
    }(exCallData[i].data);
    require(success, "Call Failed");
    results[i] = ret;
  }
}
```

## Recommendation
Consider adding a whitelist for contract address and function selectors that admin can do the external to. Or applying a timelock to prevent admin abused their power.
