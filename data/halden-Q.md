## [L-01] Add additional check for setting of exchangeRateDeltaLimit
It should be good to add additional check for what value can be settled for exchangeRateDeltaLimit. The variable is in basis points, so some biggest value for that variable can lead to unexpected behavior.
File CashManager.sol: [396](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L396)

## [L-02] Not good practice for adding hardcoded address for external contract
I recomend to be given address as input in the constructor instead of hardcoded address of external contract in the code.
File OndoPriceOracle.sol [42](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L42)

## [L-03] Move onlyGuardian and roles in abstract contract for Factory contract to make file smaller
The modifier onlyGuardian and roles can be moved in abstarct class for all factory contract. It will make file smaller and easy for reading. Other things that repeat can also be moved.

## [L-04] Skip using of v!=27 && V!=28 or v==27 || v=29
File KYCRegistry.sol [87](https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L87)
See this for reference:
https://twitter.com/alexberegszaszi/status/1534461421454606336?s=20&t=H0Dv3ZT2bicx00hLWJk7Fg

Best approach:
```
// Whether v is 27 or 28.
                // The magic number has the 27th and 28th bytes
                // counting from the most significant byte set to 1.
                vIsValid := byte(v, 0x0101000000)
```