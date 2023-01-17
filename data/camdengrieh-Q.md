Initiator address does not have TRANSFER_ROLE

CashFactory.sol -> Cash.sol

CashKYCSenderFactory.sol -> Cash.sol

CashKYCSenderReceiverFactory.sol -> Cash.sol


In the Read Me document covering Cash Tokens, it says that "Initiator address has the `TRANSFER_ROLE`" presumably assuming this happens on deployment from the proxy or deployer of the contract, as it happens for the other roles.

However, the TRANSFER_ROLE is not assigned to any address at any given point, without an external call being made by an account with DEFAULT_ADMIN_ROLE to call the grantRole() method.



