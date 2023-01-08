## Remove unused function parameters
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L116
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L174
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L775
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L159
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L160
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L173
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L175
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L176
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L413
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L575


## Remove unused local variables
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L262
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L138
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L139
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L342
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L632
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L139


## Remove unused imports
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L17
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L24
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L18
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L21
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L6
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L4
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L25
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L33
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L53
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L58
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L59
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L24
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L18
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L34
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L35
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L36
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L38

## Use a modifier instead of repeating the same require statement
`msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN())`
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223

`require(msg.sender == owner()); //owner is "strategist"`
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211


## Use latest Solidity version with a stable pragma statement
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L2
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L2
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L2
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2

## Missing zero address checks
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L712
