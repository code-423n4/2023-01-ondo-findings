1. No validation of constructor parameters `address managerAdmin` and `address pauser` in `cashManager.sol`. These are critical addresses to the contract and must be validated.
2. No need to assign `currentEpoch` to itself in the constructor of `cashManager.sol`, it should be removed.
