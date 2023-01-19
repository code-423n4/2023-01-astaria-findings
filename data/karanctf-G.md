## [G-1] Multiple address mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate
check mannulay and only use of addresses is used as a key

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.
```solidity
interfaces/ICollateralToken.sol:60:    mapping(address => bool) flashEnabled;
interfaces/ICollateralToken.sol:64:    mapping(address => address) securityHooks;
interfaces/IVaultImplementation.sol:49:    mapping(address => bool) allowList;
interfaces/IAstariaRouter.sol:84:    mapping(address => bool) vaults;
test/TestHelpers.t.sol:1024:  mapping(address => Conduit) bidderConduits;
```



## [G-2] Use `private` for `constants`
```solidity
VaultImplementation.sol:44:  bytes32 public constant STRATEGY_TYPEHASH =
strategies/CollectionValidator.sol:32:  uint8 public constant VERSION_TYPE = uint8(2);
strategies/UniqueValidator.sol:33:  uint8 public constant VERSION_TYPE = uint8(1);
strategies/UNI_V3Validator.sol:57:  uint8 public constant VERSION_TYPE = uint8(3);
```