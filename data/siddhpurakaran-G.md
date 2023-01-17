Gas Optimizations
1. Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 8 instances of this issue:

File: contracts/lending/OndoPriceOracle.sol   #1

44  /// @notice Contract storage for fToken's underlying asset prices
45 mapping(address => uint256) public fTokenToUnderlyingPrice;
46
47  /// @notice fToken to cToken associations for piggy backing off
48  ///         of cToken oracles
49  mapping(address => address) public fTokenToCToken;

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L44-L49


File: contracts/lending/OndoPriceOracleV2.sol   #2

54  /// @notice fToken to Oracle Type association
55  mapping(address => OracleType) public fTokenToOracleType;
56
57  /// @notice Contract storage for fToken's underlying asset prices
58  mapping(address => uint256) public fTokenToUnderlyingPrice;
59
60  /// @notice fToken to cToken associations for piggy backing off
61  ///         of Compound's Oracle
62  mapping(address => address) public fTokenToCToken;
63
64  struct ChainlinkOracleInfo {
65    AggregatorV3Interface oracle;
66    uint256 scaleFactor;
67  }
68
69  /// @notice fToken to Chainlink oracle association
70  mapping(address => ChainlinkOracleInfo) public fTokenToChainlinkOracle;
71
72  /// @notice Price cap for the underlying asset of an fToken. Optional.
73  mapping(address => uint256) public fTokenToUnderlyingPriceCap;

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L54-L73


File: contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol   #3

93  // Official record of token balances for each account
94  mapping(address => uint) internal accountTokens;
95
96  // Approved token transfer amounts on behalf of others
97  mapping(address => mapping(address => uint)) internal transferAllowances;
98
99  /**
100   * @notice Container for borrow balance information
101   * @member principal Total balance (with accrued interest), after applying the most recent balance-changing action
102   * @member interestIndex Global borrowIndex as of the most recent balance-changing action
103   */
104  struct BorrowSnapshot {
105    uint principal;
106    uint interestIndex;
107  }
108
109  // Mapping of account addresses to outstanding borrow balances
110  mapping(address => BorrowSnapshot) internal accountBorrows;

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L93-L110

File: contracts/lending/tokens/cToken/CTokenInterfacesModified.sol   #4

91  // Official record of token balances for each account
92  mapping(address => uint) internal accountTokens;
93
94  // Approved token transfer amounts on behalf of others
95  mapping(address => mapping(address => uint)) internal transferAllowances;
96
97  /**
98   * @notice Container for borrow balance information
99   * @member principal Total balance (with accrued interest), after applying the most recent balance-changing action
100   * @member interestIndex Global borrowIndex as of the most recent balance-changing action
101   */
102  struct BorrowSnapshot {
103    uint principal;
104    uint interestIndex;
105  }
106
107  // Mapping of account addresses to outstanding borrow balances
108  mapping(address => BorrowSnapshot) internal accountBorrows;

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L91-L108

File: contracts/lending/tokens/cErc20ModifiedDelegator.sol   #5

270  /**
271   * @notice Official record of token balances for each account
272   */
273  mapping(address => uint256) internal accountTokens;
274
275  /**
276   * @notice Approved token transfer amounts on behalf of others
277   */
278  mapping(address => mapping(address => uint256)) internal transferAllowances;
279
280  /**
281   * @notice Container for borrow balance information
282   * @member principal Total balance (with accrued interest), after applying the most recent balance-changing action
283   * @member interestIndex Global borrowIndex as of the most recent balance-changing action
284   */
285  struct BorrowSnapshot {
286    uint256 principal;
287    uint256 interestIndex;
288  }
289
290  /**
291   * @notice Mapping of account addresses to outstanding borrow balances
291   */
292  mapping(address => BorrowSnapshot) internal accountBorrows;

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L270-L292