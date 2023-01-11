## Using bools for storage incurs overhead

### Description

Use uint256 for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

### Findings

- [src/interfaces/ILienToken.sol#L49)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L49) => `bool atLiquidation;`
- [src/interfaces/IVaultImplementation.sol#L38](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L38) => `bool allowListEnabled;`
- [src/interfaces/IVaultImplementation.sol#L46](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L46) => `bool allowListEnabled;`
- [src/interfaces/IVaultImplementation.sol#L47](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L47) => `bool isShutdown;`
- [src/LienToken.sol#L810](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L810) => `bool isPublicVault = _isPublicVault(s, lienOwner);`
- [src/VaultImplementation.sol#L399](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L399) => `bool feeOn = feeTo != address(0);`

### References

- [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)