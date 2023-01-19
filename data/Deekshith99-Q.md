# QA Findings

## [NC-1] Unused imports are used
There are 15 instances of it 
its recommend to remove unused imports from the contracts for better readability of the code
here are the permalinks of them
[ClearingHouse.sol#L17](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L17),
[ClearingHouse.sol#L24-L26](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L24-L26),
[CollateralToken.sol#L33](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L33),
[CollateralToken.sol#L59](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L59),
[PublicVault.sol#L24](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L24),
[PublicVault.sol#L27](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L27),
[AstariaRouter.sol#L42](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L42),
[LienToken.sol#L34](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L34),
[AstariaVaultBase.sol#L18](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L18),
[AstariaVaultBase.sol#L21](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L21),
[VaultImplementation.sol#L25](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L25),
[IPublicVault.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IPublicVault.sol#L16),
[ICollateralToken.sol#L27](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ICollateralToken.sol#L27),
[IAstariaRouter.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L16),
[IAstariaRouter.sol#L25](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L25)

## [NC-2] NatSpec is missing
it could be better if they add natspec comments in this contract 
here is the permalink of the contract
[ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

## [NC-3] Wrong comment 
here is the permalink of it [AstariaRouter.sol#L73](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L73)

`uint88 maxInterestRate; //6` 
it should be as 
`uint88 maxInterestRate; //11`
because in the context  for the uint88 converts as 11 bytes 
so instead of 6 it should replace with 11
