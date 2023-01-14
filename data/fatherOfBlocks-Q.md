**src/Vault.sol**
- L65/71 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/ClearingHouse.sol**
- L17/25 - Two classes are imported (WETH and ConduitControllerInterface) that are never used, therefore they should be removed.

- L72/199/216/223 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/CollateralToken.sol**
- L33/53/59 - Two classes are imported (VaultImplementation, SeaportInterface and OrderComponents) that are never used, therefore they should be removed.

- L266/535/564 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/PublicVault.sol**
- L240 - IERC4626 is imported but never used, so it should be removed.

- L241/259/508/672/680/687 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/AstariaRouter.sol**
- L341/347/354/361 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/LienToken.sol**
- L18/34 - Two classes are imported (Auth and VaultImplementation) that are never used, therefore they should be removed.

- L504/860 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/AstariaVaultBase.sol**
- L18/21 - Two classes are imported (IERC4626 and IRouterBase) that are never used, therefore they should be removed.


**lib/gpl/src/ERC20-Cloned.sol**
- L4 - IERC20 is imported but never used, so it should be removed.


**src/VaultImplementation.sol**
- L25 - IPublicVault is imported but never used, so it should be removed.

- L78/96/105/114/147/191/211 - When a require is used, a message should be put in case it is reverted to inform the user.
In these cases that does not happen.


**src/interfaces/IAstariaRouter.sol**
- L16/18/25 - Two classes are imported (IERC721, IERC4626 and IERC4626RouterBase) that are never used, therefore they should be removed.

- L33/37 - Within the FileType enum there are two values ​​that are never used, MinInterestRate and StrategistFee, therefore they should be eliminated.


**src/interfaces/ILienToken.sol**
- L328/331/332 - Within the InvalidStates enum there are two values ​​that are never used, NOT_ENOUGH_FUNDS, LIEN_NO_DEBT and COLLATERAL_NOT_DEPOSITED, therefore they should be eliminated.


**src/interfaces/ICollateralToken.sol**
- L27 - IERC1155 is imported but never used, so it should be removed.

- L171 - An error is created that is never used, ListPriceTooLow, therefore it should be eliminated.


**src/interfaces/IVaultImplementation.sol**
- L60 - The IncrementNonce event is created but it is never used, therefore it should be removed.
