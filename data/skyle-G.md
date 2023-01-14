G-01 internal functions only called once can be inlined to save gas

_min
contracts/lending/OndoPriceOracleV2.sol line 324 
_scaleUp
contracts/cash/CashManager.sol

G-02 Usage of UINT/INT smaller than 32 bytes incurs overhead

contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol line 30
contracts/lending/tokens/cToken/CTokenInterfacesModified.sol line 30

G-03 reduce unnecessary variable

amountE24
contracts/cash/CashManager.sol line 491