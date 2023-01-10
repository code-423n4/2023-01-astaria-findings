## [QA-01] Remove unused function parameters
ClearingHouse.sol: [82](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82), [90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90), [106](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106), [115](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115), [116](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L116), [174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L174)

LienToken.sol: [775](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L775)

CollateralToken.sol: [159](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L159), [160](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L160), [173](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L173), [175](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L175), [176](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L176)

PublicVault.sol: [413](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L413), [575](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L575)

## [QA-02] Remove unused local variables
PublicVault.sol: [262](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L262)

CollateralToken.sol: [138](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L138), [139](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L139), [342](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L342)

LienToken.sol: [632](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L632)

ClearingHouse.sol: [139](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L139)

## [QA-03] Use a modifier instead of repeating the same require statement
`msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN())`
ClearingHouse.sol: [199](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199), [216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216), [223](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223)

`require(msg.sender == owner()); //owner is "strategist"`
VaultImplementation.sol: [78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78), [96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96), [105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105), [114](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114), [147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147), [211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211)

## [QA-04] Use latest Solidity version with a stable pragma statement
ERC4626Router.sol: [2](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L2)

ERC4626RouterBase.sol: [2](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L2)

ERC4626-Cloned.sol: [2](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L2)

ERC721.sol: [2](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2)

## [QA-05] Missing zero address checks
AstariaRouter.sol: [339](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339), [712](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L712)

## [QA-06] `initialize` function can be called by anybody
AstariaRouter.sol : [83](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83)

CollateralToken.sol: [80](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80)

LienToken.sol: [59](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59)