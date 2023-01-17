### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove the `initializer` modifier |28 |
| [G-02] |Use hardcode address instead ``address(this)``| 50 |
| [G-03] |Duplicated require()/if() checks should be refactored to a modifier or function |17 |
| [G-04] | Using ``delete`` instead of setting state variable/mapping to ``0`` saves gas  |5 |
| [G-05] | Using ``delete`` instead of setting ``address(0)`` saves gas |6|
| [G-06] |Updating the `currentEpoch`` state variable again wastes gas  |1 |
| [G-07] |Unnecessary computation | 7 |
| [G-08] | Don't use _msgSender() if not supporting EIP-2771 | 3 |
| [G-09] | Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statemen  | 2 |
| [G-10] | Empty blocks should be removed or emit something  | 3 |
| [G-11] | Use ``require`` instead of ``assert``  |3 |
| [G-12] |Use ``assembly`` to write _address storage values_   | 28 |
| [G-13] | Setting the _constructor_ to `payable`  | 14 |
| [G-14] |Use ``double require`` instead of using ``&&``  | 3 |
| [G-15] |Sort Solidity operations using short-circuit mode | 2 |
| [G-16] |`x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables | 3 |
| [G-17] |Upgrade Solidity's optimizer |  |
| [G-18] |Optimize names to save gas|All contracts |
| [G-19] |Use a more recent version of solidity  | 27 |

Total 19 issues


### [G-01] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

2 results - 2 files:
```solidity
contracts\cash\token\CashKYCSenderReceiver.sol:
  46:   function initialize(
  51   ) public initializer {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L46


```solidity
contracts\cash\token\CashKYCSenderReceiver.sol:
  46:   function initialize(
  51   ) public initializer {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L46

In the EVM, the constructor's job is actually to return the bytecode that will live at the contract's address. So, while inside a constructor, your address `(address(this))` will be the deployment address, but there will be no bytecode at that address! So if we check `address(this).code.length` before the constructor has finished, even from within a delegatecall, we will get 0. So now let's update our `initialize()` function to only run if we are inside a constructor:


```diff
contracts\cash\token\CashKYCSenderReceiver.sol:
  46:   function initialize(
- 51   ) public initializer {
+        require(address(this).code.length == 0, 'not in constructor’);
```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

### [G-02] Use hardcode address instead ``address(this)``

Instead of ``address(this)``, it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's ``script.sol`` and solmate````LibRlp.sol`` contracts can do this.

Reference:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824


50 results - 8 files:
```solidity
contracts\cash\factory\CashFactory.sol:
  92:     cashProxied.revokeRole(MINTER_ROLE, address(this));
  93:     cashProxied.revokeRole(PAUSER_ROLE, address(this));
  94:     cashProxied.revokeRole(DEFAULT_ADMIN_ROLE, address(this));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L92-L94

```solidity
contracts\cash\factory\CashKYCSenderFactory.sol:
  101:    cashKYCSenderProxied.revokeRole(MINTER_ROLE, address(this));
  102:    cashKYCSenderProxied.revokeRole(PAUSER_ROLE, address(this));
  103:    cashKYCSenderProxied.revokeRole(DEFAULT_ADMIN_ROLE, address(this));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L101-L103


```solidity
contracts\cash\factory\CashKYCSenderReceiverFactory.sol:
  101:    cashKYCSenderReceiverProxied.revokeRole(MINTER_ROLE, address(this));
  102:    cashKYCSenderReceiverProxied.revokeRole(PAUSER_ROLE, address(this));
  103:    cashKYCSenderReceiverProxied.revokeRole(DEFAULT_ADMIN_ROLE, address(this));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L101-L103


```solidity
contracts\lending\tokens\cErc20ModifiedDelegator.sol:
  1235:   (bool success, bytes memory returnData) = address(this).staticcall(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L1235


```solidity
contracts\lending\tokens\cCash\CCash.sol:
  159:    uint256 balance = token.balanceOf(address(this));
  181:    return token.balanceOf(address(this));
  200:    uint balanceBefore = EIP20Interface(underlying_).balanceOf(address(this));
  201:    token.transferFrom(from, address(this), amount);
  223:    uint balanceAfter = EIP20Interface(underlying_).balanceOf(address(this));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L159


```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
   102:   uint allowed = comptroller.transferAllowed(address(this), src, dst, tokens);
   496:   uint allowed = comptroller.mintAllowed(address(this), minter, mintAmount);
   540:   emit Transfer(address(this), minter, mintTokens);
   615:   address(this),
   653:   emit Transfer(redeemer, address(this), redeemTokens);
   658:   address(this),
   685:   address(this),
   778:   address(this),
   878:   address(this),
   927:   address(this),
   943:   if (address(cTokenCollateral) == address(this)) {
   944:   seizeInternal(address(this), liquidator, borrower, seizeTokens);
  1002:   address(this),
  1048:   emit Transfer(borrower, address(this), protocolSeizeTokens);
  1049:   emit ReservesAdded(address(this), protocolSeizeAmount, totalReservesNew);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L102


```solidity
contracts\lending\tokens\cToken\CErc20.sol:
  159:    uint256 balance = token.balanceOf(address(this));
  181:    return token.balanceOf(address(this));
  200:    uint balanceBefore = EIP20Interface(underlying_).balanceOf(address(this));
  201:    token.transferFrom(from, address(this), amount);
  223:    uint balanceAfter = EIP20Interface(underlying_).balanceOf(address(this));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L159


```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
   102:   uint allowed = comptroller.transferAllowed(address(this), src, dst, tokens);
   496:   uint allowed = comptroller.mintAllowed(address(this), minter, mintAmount);
   540:   emit Transfer(address(this), minter, mintTokens);
   615:   address(this),
   653:   emit Transfer(redeemer, address(this), redeemTokens);
   658:   address(this),
   685:   address(this),
   778:   address(this),
   878:   address(this),
   927:   address(this),
   943:   if (address(cTokenCollateral) == address(this)) {
   944:   seizeInternal(address(this), liquidator, borrower, seizeTokens);
  1005:   address(this),
  1051:   emit Transfer(borrower, address(this), protocolSeizeTokens);
  1052:   emit ReservesAdded(address(this), protocolSeizeAmount, totalReservesNew);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L102


### [G-03] Duplicated require()/if() checks should be refactored to a modifier or function

17 results 7 files:
```solidity
src/AstariaRouter.sol:
  341:    require(msg.sender == s.guardian);
  347:    require(msg.sender == s.guardian);
```
[AstariaRouter.sol#L341](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L341), [AstariaRouter.sol#L347](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L347)


```solidity
src/ClearingHouse.sol:
  199:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  216:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  223:    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
```
[ClearingHouse.sol#L199](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199), [ClearingHouse.sol#L216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216), [ClearingHouse.sol#L223](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223)


```solidity
src/PublicVault.sol:
  508:    require(msg.sender == owner()); //owner is "strategist»
```
[PublicVault.sol#L508](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L508)


```solidity
src/Vault.sol:
  71:     require(msg.sender == owner());
```
[Vault.sol#L71](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L71)


```solidity
src/VaultImplementation.sol:
   78:    require(msg.sender == owner()); //owner is "strategist"
   96:    require(msg.sender == owner()); //owner is "strategist"
  105:    require(msg.sender == owner()); //owner is "strategist"
  114:    require(msg.sender == owner()); //owner is "strategist"
  147:    require(msg.sender == owner()); //owner is "strategist"
  211:    require(msg.sender == owner()); //owner is "strategist»
```
[VaultImplementation.sol#L78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78), [VaultImplementation.sol#L96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96), [VaultImplementation.sol#L105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105), [VaultImplementation.sol#L114](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114), [VaultImplementation.sol#L147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147), [VaultImplementation.sol#L211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211)

```solidity
contracts\lending\tokens\cCash\CCashDelegate.sol:
  26:     if (false) {
  27:       implementation = address(0);
  28:     }

  41:     if (false) {
  42:       implementation = address(0);
  43:     }
```
[CCashDelegate.sol#L26-L28](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L26-L28), [CCashDelegate.sol#L41-L43](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L41-L43)

```solidity
contracts\lending\tokens\cToken\CTokenDelegate.sol:
  26:     if (false) {
  27:       implementation = address(0);
  28:     }
  
  41:     if (false) {
  42:       implementation = address(0);
  43:     }
```
[CTokenDelegate.sol#L26-L28](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L26-L28), [CTokenDelegate.sol#L41-L43](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L41-L43)


**Recommendation:**

You can consider adding a modifier like below.

```solidity
 modifer checkOwner () {
        require(require(msg.sender == owner());
        _;
 }
```

Here are the data available in the covered contracts. The use of this situation in contracts that are not covered will also provide gas optimization.

### [G-04] Using ``delete`` instead of setting state variable/mapping to ``0`` saves gas

5 results - 1 file:
```solidity
contracts\cash\CashManager.sol:
  259:     mintRequestsPerEpoch[epochToClaim][user] = 0;

  580:     currentRedeemAmount = 0;

  581:     currentMintAmount = 0;

  754:     redemptionInfoPerEpoch[epochToService].addressToBurnAmt[redeemer] = 0;

  790:     redemptionInfoPerEpoch[epochToService].addressToBurnAmt[refundee] = 0;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L259


```diff
contracts\cash\CashManager.sol#L259
- 259:     mintRequestsPerEpoch[epochToClaim][user] = 0;
+             delete mintRequestsPerEpoch[epochToClaim][user];
```

### [G-05] Using ``delete`` instead of setting ``address(0)`` saves gas

6 results - 4 files:
```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
  1099:    pendingAdmin = payable(address(0));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1099


```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
  1102:    pendingAdmin = payable(address(0));
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1102


```solidty
contracts\lending\tokens\cCash\CCashDelegate.sol:
  27:      implementation = address(0);

  42:      implementation = address(0);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L27


```solidity
contracts\lending\tokens\cToken\CTokenDelegate.sol:
  27:      implementation = address(0);

  42:      implementation = address(0);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L27


**Proof Of Concepts:**
```solidity
contract Example {
    address public myAddress = 0x5B38Da6a701c568545dCfcB03FcB875f56beddC4;

    //  use for empty value Slot:     23450 gas
    //  use for non empty value Slot: 21450 gas
    function reset() public {
        delete myAddress;
    }


    // use for empty value Slot:     23497 gas
    // use for non empty value Slot: 23497 gas
    function setToZero() public {
        myAddress = address(0);
    }
}
```

### [G-06] Updating the `currentEpoch`` state variable again wastes gas

Updating the `currentEpoch`` state variable again wastes gas.

Interestingly, in the constructor of the ``CashManager.sol`` contract, the ``currentEpoch` value is updated again with the ``currentEpoch` value. This value will not change anything and will cause an additional SLOAD usage.

```solidty
contracts\cash\CashManager.sol:
  127   constructor(
  168:     currentEpoch = currentEpoch;
  183   }
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L168


**Recommendation:**
If no other value will be assigned, I suggest you delete the relevant row.



### [G-07] Unnecessary computation

When emitting an event that includes a new and an old value, it is cheaper in gas to avoid caching the old value in memory. Instead, emit the event, then save the new value in storage.

```solidity
contracts\cash\CashManager.sol:
  452   function setFeeRecipient(
  453     address _feeRecipient
  454   ) external override onlyRole(MANAGER_ADMIN) {
  455:    address oldFeeRecipient = feeRecipient;
  456:    feeRecipient = _feeRecipient;
  457:    emit FeeRecipentSet(oldFeeRecipient, _feeRecipient);
  458   } 


  465   function setAssetRecipient(
  466     address _assetRecipient
  467   ) external override onlyRole(MANAGER_ADMIN) {
  468:     address oldAssetRecipient = assetRecipient;
  469:     assetRecipient = _assetRecipient;
  470:     emit AssetRecipientSet(oldAssetRecipient, _assetRecipient);
  471   }


  546   function setEpochDuration(
  547     uint256 _epochDuration
  548   ) external onlyRole(MANAGER_ADMIN) {
  549:     uint256 oldEpochDuration = epochDuration;
  550:     epochDuration = _epochDuration;
  551:     emit EpochDurationSet(oldEpochDuration, _epochDuration);
  552   }


  596   function setMintLimit(uint256 _mintLimit) external onlyRole(MANAGER_ADMIN) {
  597:     uint256 oldMintLimit = mintLimit;
  598:     mintLimit = _mintLimit;
  599:     emit MintLimitSet(oldMintLimit, _mintLimit);
  600   }


  609   function setRedeemLimit(
  610     uint256 _redeemLimit
  611   ) external onlyRole(MANAGER_ADMIN) {
  612:     uint256 oldRedeemLimit = redeemLimit;
  613:     redeemLimit = _redeemLimit;
  614:     emit RedeemLimitSet(oldRedeemLimit, _redeemLimit);
  615   }


  803   function setAssetSender(
  804     address newAssetSender
  805   ) external onlyRole(MANAGER_ADMIN) {
  806:     address oldAssetSender = assetSender;
  807:     assetSender = newAssetSender;
  808:     emit AssetSenderSet(oldAssetSender, newAssetSender);
  809   }


  817   function setRedeemMinimum(
  818     uint256 newRedeemMinimum
  819   ) external onlyRole(MANAGER_ADMIN) {
  820:     uint256 oldRedeemMin = minimumRedeemAmount;
  821:     minimumRedeemAmount = newRedeemMinimum;
  822:     emit MinimumRedeemAmountSet(oldRedeemMin, minimumRedeemAmount);
  823   }
```
[CashManager.sol#L455-L457](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L455-L457), [CashManager.sol#L468-L470](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L468-L470), [CashManager.sol#L549-L551](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L549-L551), [CashManager.sol#L597-L599](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L597-L599), [CashManager.sol#L612-L614](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L612-L614), [CashManager.sol#L806-L808](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L806-L808), [CashManager.sol#L820-L822](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L820-L822)


**Recommendation:**
```diff
contracts\cash\CashManager.sol#L457

  452   function setFeeRecipient(
  453     address _feeRecipient
  454   ) external override onlyRole(MANAGER_ADMIN) {
- 455:    address oldFeeRecipient = feeRecipient;
+ 457:    emit FeeRecipientSet(oldFeeRecipient, _feeRecipient);
  456:    feeRecipient = _feeRecipient;
- 457:    emit FeeRecipientSet(oldFeeRecipient, _feeRecipient);
  458   }
  ```


### [G-08] Don't use _msgSender() if not supporting EIP-2771

Use ``msg.sender`` if the code does not implement EIP-2771 trusted forwarder support.

Reference: https://eips.ethereum.org/EIPS/eip-2771

3 results - 3 files:
```solidity
contracts\cash\token\Cash.sol:
  36      require(
  37:       hasRole(TRANSFER_ROLE, _msgSender()),
  38        "Cash: must have TRANSFER_ROLE to transfer"
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L37

```solidity
contracts\cash\token\CashKYCSender.sol:
  63      require(
  64:       _getKYCStatus(_msgSender()),
  65        "CashKYCSender: must be KYC'd to initiate transfer"
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L64


```solidity
contracts\cash\token\CashKYCSenderReceiver.sol:
  63      require(
  64:       _getKYCStatus(_msgSender()),
  65        "CashKYCSenderReceiver: must be KYC'd to initiate transfer"
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L64


### [G-09] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement

```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.

2 results - 1 file:
```solidity
contracts\cash\CashManager.sol:
  864:     if (balance < previousBalance) {
  865       redemptionInfoPerEpoch[epoch].totalBurned -= previousBalance - balance;
  866:     } else if (balance > previousBalance) {
  867       redemptionInfoPerEpoch[epoch].totalBurned += balance - previousBalance;
  868     }
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L864-L868

```solidity
contracts\cash\CashManager.sol:
  296:     if (exchangeRate > lastSetMintExchangeRate) {
  297        rateDifference = exchangeRate - lastSetMintExchangeRate;
  298:     } else if (exchangeRate < lastSetMintExchangeRate) {
  299        rateDifference = lastSetMintExchangeRate - exchangeRate;
  300     }
```


```diff
contracts\cash\CashManager.sol:
+        unchecked {
  296:    if (exchangeRate > lastSetMintExchangeRate) {
  297       rateDifference = exchangeRate - lastSetMintExchangeRate;
  298:    } else if (exchangeRate < lastSetMintExchangeRate) {
  299         rateDifference = lastSetMintExchangeRate - exchangeRate;
  300     }
+        }
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L296-L300


### [G-10] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

3 results - 3 files:
```solidity
contracts\cash\Proxy.sol:
  25:   ) TransparentUpgradeableProxy(_logic, _admin, _data) {}
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L25


```solidity
contracts\lending\tokens\cCash\CCashDelegate.sol:
  15:   constructor() {}
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L15


```solidity
contracts\lending\tokens\cToken\CTokenDelegate.sol:
  15:   constructor() {}
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L15


### [G-11] Use ``require`` instead of ``assert``

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

3 results 3 files:
```solidity
contracts\cash\factory\CashFactory.sol:
   75   function deployCash(
   97:     assert(cashProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97

```solidity
contracts\cash\factory\CashKYCSenderFactory.sol:
   75   function deployCashKYCSender(
  106:     assert(cashKYCSenderProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106

```solidity
contracts\cash\factory\CashKYCSenderReceiverFactory.sol:
   75   function deployCashKYCSenderReceiver(
  106:     assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106


### [G-12] Use ``assembly`` to write _address storage values_ 

28 result - 13 files:
```solidity
contracts\cash\factory\CashFactory.sol:
  53     constructor(address _guardian) {
  54:       guardian = _guardian;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L54

```solidity
contracts\cash\factory\CashKYCSenderFactory.sol:
  53     constructor(address _guardian) {
  54:       guardian = _guardian;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L54

```solidity
contracts\cash\factory\CashKYCSenderReceiverFactory.sol:
  53     constructor(address _guardian) {
  54:       guardian = _guardian;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54

```solidity
contracts\cash\kyc\KYCRegistryClient.sol:
  39     function _setKYCRegistry(address _kycRegistry) internal {
  44:       kycRegistry = IKYCRegistry(_kycRegistry);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L44

```solidity
contracts\cash\CashManager.sol:
  127    constructor(
  165:      feeRecipient = _feeRecipient;
  166:      assetRecipient = _assetRecipient;
  167:      assetSender = _assetSender;

  452    function setFeeRecipient(
  456:      feeRecipient = _feeRecipient;

  465    function setAssetRecipient(
  469:      assetRecipient = _assetRecipient;

  803    function setAssetSender(
  807:      assetSender = newAssetSender;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L165-L167

```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
  1060   function _setPendingAdmin(
  1072:     pendingAdmin = newPendingAdmin;

  1085   function _acceptAdmin() external override returns (uint) {
  1096:     admin = pendingAdmin;

  1112   function _setComptroller(
  1125:     comptroller = newComptroller;

  1314   function _setInterestRateModelFresh(
  1340:     interestRateModel = newInterestRateModel;

  1388   function _setKYCRegistry(address _kycRegistry) internal {
  1391:     kycRegistry = IKYCRegistry(_kycRegistry);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1072

```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
  1063   function _setPendingAdmin(
  1075:     pendingAdmin = pendingAdmin;

  1088   function _acceptAdmin() external override returns (uint) {
  1099:     admin = pendingAdmin; 

  1115   function _setComptroller(
  1128:     comptroller = newComptroller;

  1317   function _setInterestRateModelFresh(
  1343:     interestRateModel = newInterestRateModel;

  1391   function _setKYCRegistry(address _kycRegistry) internal {
  1394:     kycRegistry = IKYCRegistry(_kycRegistry);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1075

```solidity
contracts\lending\tokens\cCash\CCash.sol:
  30     function initialize(
  54:        underlying = underlying_;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L54

```solidity
contracts\lending\tokens\cToken\CErc20.sol:
  30     function initialize(
  54:       underlying = underlying_;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L54

```solidity
contracts\lending\tokens\cErc20ModifiedDelegator.sol:
  673    constructor(
  711:      admin = admin_;

  720    function _setImplementation(
  737:      implementation = implementation_;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L711

```solidity
contracts\lending\OndoPriceOracle.sol:
  106    function setOracle(address newOracle) external override onlyOwner {
  108:      cTokenOracle = CTokenOracle(newOracle);

  119    function _setFTokenToCToken(address fToken, address cToken) internal {
  124:      fTokenToCToken[fToken] = cToken;   
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L108

```solidity
contracts\lending\OndoPriceOracleV2.sol:
  182    function setOracle(address newOracle) external override onlyOwner {
  184:      cTokenOracle = CTokenOracle(newOracle);
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L184

```solidity
contracts\lending\JumpRateModelV2.sol:
  59     constructor(
  66:       owner = owner_;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L66


**Recommendation Code:**
```solidity
contracts\lending\JumpRateModelV2.sol#L66
 59     constructor(
 66:       owner = owner_;
                  assembly {
                    sstore(entryPoint.slot, _entryPoint)
                  }
```

### [G-13] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

14 results - 14 files:
```solidity
contracts\cash\CashManager.sol:
  127:   constructor(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L127


```solidity
contracts\cash\Proxy.sol:
  21:   constructor(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L21


```solidity
contracts\cash\factory\CashFactory.sol:
  53:   constructor(address _guardian) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L53


```solidity
contracts\cash\factory\CashKYCSenderFactory.sol:
  53:   constructor(address _guardian) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L53


```solidity
contracts\cash\factory\CashKYCSenderReceiverFactory.sol:
  53:   constructor(address _guardian) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L53


```solidity
contracts\cash\kyc\KYCRegistry.sol:
  51:   constructor(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L51


```solidity
contracts\cash\kyc\KYCRegistryClientConstructable.sol:
  36:   constructor(address _kycRegistry, uint256 _kycRequirementGroup) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L36


```solidity
contracts\cash\token\Cash.sol:
  25:   constructor() {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L25


```solidity
contracts\cash\token\CashKYCSender.sol:
  30:   constructor() {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L30


```solidity
contracts\cash\token\CashKYCSenderReceiver.sol:
  30:   constructor() {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L30


```solidity
contracts\lending\JumpRateModelV2.sol:
  59:   constructor(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L59


```solidity
contracts\lending\tokens\cErc20ModifiedDelegator.sol:
  673:   constructor(
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L673


```solidity
contracts\lending\tokens\cCash\CCashDelegate.sol:
  15:   constructor() {}
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L15


```solidity
contracts\lending\tokens\cToken\CTokenDelegate.sol:
  15:   constructor() {}
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L15


**Recommendation:**
Set the constructor to ```payable```


### [G-14] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

3 results - 3 files:
```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
  45:     require(
  46:       accrualBlockNumber == 0 && borrowIndex == 0,
  47:       "market may only be initialized once"
  48:     );
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L45-L48

 ```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
  45:     require(
  46:       accrualBlockNumber == 0 && borrowIndex == 0,
  47:       "market may only be initialized once"
  48:     );
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L45-L48

 ```solidity
contracts\lending\OndoPriceOracleV2.sol:
  292:     require(
  293       (answeredInRound >= roundId) &&
  294         (updatedAt >= block.timestamp - maxChainlinkOracleTimeDelay),
  295       "Chainlink oracle price is stale"
  296     );
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L292-L296


**Recommendation Code:**
 ```diff
contracts\lending\tokens\cCash\CTokenCash.sol#L46
   45:    require(
- 46:       accrualBlockNumber == 0 && borrowIndex == 0,
+            accrualBlockNumber == 0, "market may only be initialized once")
+        require(borrowIndex == 0, 
   47:    "market may only be initialized once"
   48:    );
```


### [G-15] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```

2 results - 2 files:
```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
  1087:     if (msg.sender != pendingAdmin || msg.sender == address(0)) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L1087

```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
  1090:     if (msg.sender != pendingAdmin || msg.sender == address(0)) {
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L1090


### [G-16] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

3 results - 1 file:
```solidity
contracts\cash\CashManager.sol:
  582:     currentEpoch += epochDifference;
  630:     currentMintAmount += collateralAmountIn;
  649:     currentRedeemAmount += amount;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L582


```diff
contracts\cash\CashManager.sol:
- 582:     currentEpoch += epochDifference;
+             currentEpoch = currentEpoch +  epochDifference;
```

`x += y (x -= y)` costs more gas than `x = x  + y (x = x - y)` for state variables.


### [G-17] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

```js
hardhat.config.ts:
  25: const config: HardhatUserConfig = {
  26:   solidity: {
  27:     compilers: [
  28:       {
  29:         version: "0.8.16",
  30:         settings: {
  31:           optimizer: {
  32:             enabled: true,
  33:             runs: 100,
  34:           },
  35:         },
  36:       },
  37:       {
  38:         version: "0.5.17",
  39:         settings: {
  40:           optimizer: {
  41:             enabled: true,
  42:             runs: 100,
  43:           },
  44:         },
  45:       },
  46:       {
  47:         version: "0.6.12",
  48:         settings: {
  49:           optimizer: {
  50:             enabled: true,
  51:             runs: 200,
  52:           },
  53:         },
  54:       },
  55:     ],
  56:   },
```

### [G-18] Optimize names to save gas
Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` CashManager.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

CashManager.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
49168470  =>  _processRefund(address[],uint256)
69414246  =>  _checkKYC(address)
49733d04  =>  requestMint(uint256)
abfbf94a  =>  claimMint(address,uint256)
da2c3df2  =>  setMintExchangeRate(uint256,uint256)
ae59a99e  =>  setPendingMintBalance(address,uint256,uint256,uint256)
36b68d01  =>  overrideExchangeRate(uint256,uint256,uint256)
93ab1386  =>  setMintExchangeRateDeltaLimit(uint256)
eddd0d9c  =>  setMintFee(uint256)
aab483d6  =>  setMinimumDepositAmount(uint256)
e74b981b  =>  setFeeRecipient(address)
14b82631  =>  setAssetRecipient(address)
44395f3a  =>  _getMintAmountForEpoch(uint256,uint256)
8e4f969b  =>  _getMintFees(uint256)
595c8b6b  =>  _scaleUp(uint256)
8456cb59  =>  pause()
3f4ba83a  =>  unpause()
30024dfe  =>  setEpochDuration(uint256)
c42dc6bd  =>  transitionEpoch()
9e6a1d7d  =>  setMintLimit(uint256)
fb81a6b0  =>  setRedeemLimit(uint256)
b46552b0  =>  _checkAndUpdateMintLimit(uint256)
79011fcc  =>  _checkAndUpdateRedeemLimit(uint256)
eeb3c910  =>  requestRedemption(uint256)
e51b5564  =>  completeRedemptions(address[],address[],uint256,uint256,uint256)
ad67a117  =>  _processRedemption(address[],uint256,uint256,uint256)
525decd6  =>  setAssetSender(address)
6f0e42b9  =>  setRedeemMinimum(uint256)
e17bbeea  =>  getBurnedQuantity(uint256,address)
75b3ef61  =>  setPendingRedemptionBalance(address,uint256,uint256)
24f09e9c  =>  setKYCRequirementGroup(uint256)
600d2dbc  =>  setKYCRegistry(address)
e986160f  =>  _checkAddressesKYC(address[])
97d8d991  =>  multiexcall(ExCallData[])
```


### [G-19] Use a more recent version of solidity

Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value. 

In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.

In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

27 results - 27 files:
```solidity
contracts\cash\CashManager.sol:
  15: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L15

```solidity
contracts\cash\Proxy.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/Proxy.sol#L16

```solidity
contracts\cash\factory\CashFactory.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L16

```solidity
contracts\cash\factory\CashKYCSenderFactory.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L16

```solidity
contracts\cash\factory\CashKYCSenderReceiverFactory.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L16

```solidity
contracts\cash\interfaces\ICashManager.sol:
  15: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/ICashManager.sol#L15

```solidity
contracts\cash\interfaces\IKYCRegistry.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistry.sol#L16

```solidity
contracts\cash\interfaces\IKYCRegistryClient.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IKYCRegistryClient.sol#L16

```solidity
contracts\cash\interfaces\IMulticall.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/interfaces/IMulticall.sol#L16

```solidity
contracts\cash\kyc\KYCRegistry.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L16

```solidity
contracts\cash\kyc\KYCRegistryClient.sol:
  20: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClient.sol#L20

```solidity
contracts\cash\kyc\KYCRegistryClientConstructable.sol:
  19: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientConstructable.sol#L19

```solidity
contracts\cash\kyc\KYCRegistryClientInitializable.sol:
  20: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistryClientInitializable.sol#L20

```solidity
contracts\cash\token\Cash.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L16

```solidity
contracts\cash\token\CashKYCSender.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L16

```solidity
contracts\cash\token\CashKYCSenderReceiver.sol:
  16: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L16

```solidity
contracts\lending\IOndoPriceOracle.sol:
  15: pragma solidity 0.6.12;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracle.sol#L15

```solidity
contracts\lending\IOndoPriceOracleV2.sol:
  15: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/IOndoPriceOracleV2.sol#L15

```solidity
contracts\lending\OndoPriceOracleV2.sol:
  15: pragma solidity 0.8.16;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L15

```solidity
contracts\lending\tokens\cCash\CCash.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCash.sol#L2

```solidity
contracts\lending\tokens\cCash\CCashDelegate.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CCashDelegate.sol#L2

```solidity
contracts\lending\tokens\cCash\CTokenCash.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenCash.sol#L2

```solidity
contracts\lending\tokens\cCash\CTokenInterfacesModifiedCash.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L2

```solidity
contracts\lending\tokens\cToken\CErc20.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CErc20.sol#L2

```solidity
contracts\lending\tokens\cToken\CTokenDelegate.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenDelegate.sol#L2

```solidity
contracts\lending\tokens\cToken\CTokenInterfacesModified.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L2

```solidity
contracts\lending\tokens\cToken\CTokenModified.sol:
  2: pragma solidity ^0.8.10;
```
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenModified.sol#L2