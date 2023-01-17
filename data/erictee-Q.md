### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
contracts/cash/factory/CashKYCSenderFactory.sol:L106    assert(cashKYCSenderProxyAdmin.owner() == guardian);

contracts/cash/factory/CashFactory.sol:L97    assert(cashProxyAdmin.owner() == guardian);

contracts/cash/factory/CashKYCSenderReceiverFactory.sol:L106    assert(cashKYCSenderReceiverProxyAdmin.owner() == guardian);

```

### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
contracts/lending/OndoPriceOracleV2.sol:L262        uint256(IERC20Like(underlying).decimals()) -

contracts/lending/OndoPriceOracleV2.sol:L263        uint256(AggregatorV3Interface(chainlinkOracle).decimals())));

contracts/cash/CashManager.sol:L181        (IERC20Metadata(_cash).decimals() -

contracts/cash/CashManager.sol:L182          IERC20Metadata(_collateral).decimals());

```

### [L-03] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/lending/JumpRateModelV2.sol:L1     pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L7     pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L138     pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L181     pragma solidity ^0.5.12;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L296     pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L302     pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L324     pragma solidity ^0.5.16;

contracts/lending/tokens/cErc20ModifiedDelegator.sol:L647     pragma solidity ^0.5.12;

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CTokenCash.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CCash.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cCash/CCashDelegate.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenInterfacesModified.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenModified.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CErc20.sol:L2     pragma solidity ^0.8.10;

contracts/lending/tokens/cToken/CTokenDelegate.sol:L2     pragma solidity ^0.8.10;

```

### [L-04] zero-address checks are missing


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L54
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L54
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L54
