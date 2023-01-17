

# Gas Optimizations Report

# Summary

This report focuses on Ondo CASH protocol, in context of various improvements that can be made in terms of gas cost.

Some of the opportunities identified for improving gas efficiency throughout the codebase of Ondo protocol are categorised into 6 main areas; with further multiple instances in each of the category.



G-01 State variables value are by default zero
	
G-02 Require is more gas efficient than assert	

G-03 Wastage of deployed gas when return is not present for returns function
	
G-04 Multiple mappings can be combine into a single one  
	
G-05 Immutable has more gas efficiency than constant
	
G-06 State variables visibility is by default internal**
		

 
# [G-01] State variable values are by default zero (04 Instances)

When value for state variable is not specified, the default value is already 0.

Declaring zero value to state variable, consumes extra gas which is not needed.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L46
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L46
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115
4.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115


# [G-02] Require is more gas efficient than assert (03 Instances)

Assert() and require() functions are similar in nature, regarding context of handling error and undone any changes made.

However, assert() consumes all the gas during the process of reverting change while require()refunds any gas remaining that was paid.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L97
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106

# [G-03] Wastage of deployed gas when return is not present for returns function (04 Instances)

Wastage of gas during deployment; when return is absent for named variable when function returns.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L123
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L133
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L133
4.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L949

# [G-04] Multiple mappings can be combine into a single one (14 Instances)

When multiple mappings are used in same function, it’s better to combined them into a single mapping of an address struct.

Combined mapping reduces storage slot per mapping and also are cheaper in terms of associated Stack operations calculation carried out.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L45
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracle.sol#L49
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L39
4.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L45
5.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L55
6.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L58
7.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L62
8.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L70
9.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/OndoPriceOracleV2.sol#L73
10.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L75
11.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L79
12.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L86
13.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L82
14.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L86


# [G-05] Immutable has more gas efficiency than constant (26 Instances)

Using immutable instead of constant, save more gas due to avoiding storage access for state variables.

Variable values are set through constructor when using immutable, which also eliminates the need of assigning initial values to state variable making them more efficient in terms of gas cost

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/Cash.sol#L22
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSender.sol#L26
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/token/CashKYCSenderReceiver.sol#L26
4.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L44
5.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L45
6.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashFactory.sol#L46
7.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L44
8.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L45
9.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L46
10.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L44
11.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L45
12.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L46
13.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/JumpRateModelV2.sol#L29
14.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/kyc/KYCRegistry.sol#L36
15.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35
16.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L38
17.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L115
18.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33
19.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36
20.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L113
21.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L89
22.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L122
23.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L123
24.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/CashManager.sol#L124
25.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L208
26.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L213

# [G-06] State variable values are by default zero (24 Instances)

When visibility for a state variable is not specified, the default value is already internal.

Declaring visibility of the variable internal consumes extra gas which is not needed.

Link to the Code:

1.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L48
2.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L48
3.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L48
4.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L17
5.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L35
6.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L38
7.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L61
8.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L94
9.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L97
10.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cCash/CTokenInterfacesModifiedCash.sol#L110
11.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L15
12.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L33
13.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L36
14.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L59
15.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L92
16.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L95
17.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cToken/CTokenInterfacesModified.sol#L108
18.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L187
19.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L208
20.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L213
21.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L238
22.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L273
23.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L278
24.	https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/lending/tokens/cErc20ModifiedDelegator.sol#L293

