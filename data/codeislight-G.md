1. reconstructing CashManager mapping variables

from

```
  // Struct representing all redemption requests in an epoch
  struct RedemptionRequests {
    // Total CASH burned in the epoch
    uint256 totalBurned;
    // Mapping from address to amount of CASH address burned
    mapping(address => uint256) addressToBurnAmt;
  }

  // Mapping from epoch to redemption info struct for that epoch
  mapping(uint256 => RedemptionRequests) public redemptionInfoPerEpoch;

  // Mapping used for getting the exchange rate during a given epoch
  mapping(uint256 => uint256) public epochToExchangeRate;

  // Nested mapping containing mint requests for an epoch
  // { <epoch> : {<user> : <collateralAmount> }
  mapping(uint256 => mapping(address => uint256)) public mintRequestsPerEpoch;
```
to:
```
  // Struct representing all requests in an epoch
    struct Request {
        uint128 mintAmt;
        uint128 burnAmt;
    }
    // Struct representing all information in an epoch
    struct Info {
        uint128 exchangeRate;
        uint128 totalBurned;
    }

    mapping(uint256 => mapping(address => Request)) public requestsPerEpoch;

    mapping(uint256 => Info) public infoPerEpoch;
```

2. CashManager requestManager needs to check if there is any fees for feesInCollateral before transferring.

```
    function requestMint(uint256 collateralAmountIn)
	…
        uint256 feesInCollateral = _getMintFees(collateralAmountIn);
        ...
        if (feesInCollateral > 0)
            collateral.safeTransferFrom(
                msg.sender,
                feeRecipient,
                feesInCollateral
            );
```
