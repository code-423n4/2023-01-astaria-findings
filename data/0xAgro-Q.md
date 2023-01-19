# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Inconsistent Astaria LOGO Format|All Logo Contracts|
|[NC-02]|NatSpec Comment Used For Logo|All Logo Contracts|
|[NC-03]|Long Lines (> 120 Characters)|40|
|[NC-04]|Contracts Missing `@title` NatSpec Tag|30|
|[NC-05]|Inconsistent Comment Initial Spacing|21|
|[NC-06]|Inconsistent Named Returns|14|
|[NC-07]|Contract Layout Voids Solidity Docs|13|
|[NC-08]|Order of Functions Not Compliant With Solidity Docs|11|
|[NC-09]|Modifiers/Functions Should Replace Duplicate `require` Statements|5|
|[NC-10]|No License Indication|4|
|[NC-11]|`acheive` Spelling Mistake|2|
|[NC-12]|`nft` vs `NFT`|2|
|[NC-13]|Use of `pragma experimental ABIEncoderV2;`|2|
|[NC-14]|Inconsistent Code Style For Similar Code|2|
|[NC-15]|Extra Space in Comment|1|
|[NC-16]|Power of Ten Literal > `10e3` Not In Scientific Notation|1|
|[NC-17]|Duplicate Code|1|
|[NC-18]|Empty Comment|1|
|[NC-19]|`require` Matches Existing Modifier|1|
|[L-01]|Lack of `events`|All Contracts|

### [NC-01] Inconsistent Astaria LOGO Format

Some contracts start a newline with a space in the Astaria Logo at the top of the contract, some do not. Consider sticking to a single style.

Style `A`: [L3-L12](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L3-L12)
Style `B`: [L3-L12](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L3-L12)

Style `A` Contracts: [BeaconProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol), [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol), [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol), [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol), [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol), [AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaVaultBase.sol), [IBeacon.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IBeacon.sol), [ISecurityHook.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ISecurityHook.sol), [IRouterBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IRouterBase.sol), [ITransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ITransferProxy.sol), [IFlashAction.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IFlashAction.sol), [IStrategyValidator.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IStrategyValidator.sol), [IAstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaVaultBase.sol), [IWithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol), [IVaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol), [IPublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol), [ICollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol), [IAstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol), [ILienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol).
Style `B` Contracts: [TransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/TransferProxy.sol), [Vault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol), [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol), [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol), [WithdrawVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawVaultBase.sol), [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol).

### [NC-02] NatSpec Comment Used For Logo

The Astaria Logo in all related contracts uses the NatSpec comment `/**` instead of the standard `/*`.  `/**` is [losely reserved for NatSpec comments](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#documentation-example). Consider using the standard `/*` comment for the Astaria Logo.

### [NC-03] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*src/WithdrawProxy.sol*
*  [54](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L54), [56](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L56), [256](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L256). 

*src/CollateralToken.sol*
*  [349](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L349). 

*src/PublicVault.sol*
*  [476](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L476). 

*src/AstariaRouter.sol*
*  [75](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L75). 

*src/LienToken.sol*
*  [42](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L42), [804](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L804). 

*src/VaultImplementation.sol*
*  [226](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L226), [281](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L281), [282](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L282), [363](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L363), [376](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L376). 

*src/interfaces/IERC1155Receiver.sol*
*  [49](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC1155Receiver.sol#L49). 

*src/interfaces/IWithdrawProxy.sol*
*  [27](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L27), [33](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L33), [35](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L35), [52](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L52), [64](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L64), [74](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol#L74). 

*lib/gpl/src/interfaces/IERC4626RouterBase.sol*
*  [10](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L10), [15](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L15). 

*lib/gpl/src/interfaces/IUniswapV3PoolState.sol*
*  [34](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L34), [38](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L38), [58](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L58), [60](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L60), [101](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L101), [104](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L104), [105](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L105). 

*src/interfaces/IPublicVault.sol*
*  [79](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L79), [145](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L145), [146](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol#L146). 

*src/interfaces/ICollateralToken.sol*
*  [100](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol#L100), [161](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol#L161). 

*src/interfaces/IAstariaRouter.sol*
*  [224](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L224), [232](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol#L232). 

*src/interfaces/ILienToken.sol*
*  [181](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L181), [193](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L193), [263](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L263), [272](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol#L272).

### [NC-04] Contracts Missing `@title` NatSpec Tag

30 out of 45 of the contracts in scope are missing a `@title` tag. Given that 15 contracts all have a `@title` tag, consider adding one per the 30 remaining contracts.

[TransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/TransferProxy.sol), [BeaconProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol), [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol), [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol), [WithdrawVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawVaultBase.sol), [AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaVaultBase.sol), [ERC4626-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol), [ERC20-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol), [ERC721.sol](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol), [IBeacon.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IBeacon.sol), [IERC165.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC165.sol), [ISecurityHook.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ISecurityHook.sol), [IRouterBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IRouterBase.sol), [IERC20Metadata.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC20Metadata.sol), [ITransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ITransferProxy.sol), [IFlashAction.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IFlashAction.sol), [IStrategyValidator.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IStrategyValidator.sol), [IAstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaVaultBase.sol), [IERC1155Receiver.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC1155Receiver.sol), [IERC20.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC20.sol), [IWithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol), [IERC721.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC721.sol), [IERC1155.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC1155.sol), [IERC4626.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC4626.sol), [IVaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol), [IPublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol), [IV3PositionManager.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IV3PositionManager.sol), [ICollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol), [IAstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol), and [ILienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol) are missing a `@title` tag.

### [NC-05] Inconsistent Comment Initial Spacing

Some comments have an initial space after `//` or `///` while others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have initial space comments (EX. `// foo`): [BeaconProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol), [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol), [WithdrawVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawVaultBase.sol), [ERC4626Router.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol), [ERC4626RouterBase.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol), [ERC4626-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol), [ERC20-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol), [ERC721.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol), [IBeacon.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IBeacon.sol), [IERC165.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC165.sol), [ISecurityHook.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ISecurityHook.sol), [IMulticall.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IMulticall.sol), [IRouterBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IRouterBase.sol), [IERC721Receiver.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC721Receiver.sol), [ITransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ITransferProxy.sol), [IFlashAction.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IFlashAction.sol), [IStrategyValidator.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IStrategyValidator.sol), [IAstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaVaultBase.sol), [IERC1155Receiver.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC1155Receiver.sol), [IERC20.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC20.sol), [IERC4626Router.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626Router.sol), [IWithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IWithdrawProxy.sol), [IUniswapV3Factory.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3Factory.sol), [IERC4626RouterBase.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol), [IERC1155.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC1155.sol), [IUniswapV3PoolState.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol), [IERC4626.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC4626.sol), [IVaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IVaultImplementation.sol), and [IPublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol).
2. No contracts have only no initial space comments (EX. `//foo`).
3. The following contracts have both: [TransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/TransferProxy.sol), [Vault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol), [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol), [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol), [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol), [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol), [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol), [AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaVaultBase.sol), [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol), [ICollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol), [IAstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol), and [ILienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol).

### [NC-06] Inconsistent Named Returns

Some functions use named returns and others do not. It is best for code clearity to keep a consistent style.

1. The following contracts only have named returns (EX. `returns(uint256 foo)`): [ERC4626Router.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol), and [ERC4626RouterBase.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol).
2. The following contracts only have non-named returns (EX. `returns(uint256)`): [BeaconProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol), [Vault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol), [WithdrawVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawVaultBase.sol), and [AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaVaultBase.sol).
3. The following contracts have both: [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol), [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol), [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol), [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol), [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol), [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol), [ERC4626-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol), [ERC20-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol), [ERC721.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol), and [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol).

### [NC-07] Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol): State is positioned after Events.
* [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol): Modifiers are positioned after Functions.
* [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol): Events are positioned after Functions.
* [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol): Modifiers are positioned after Functions.
* [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol): Modifiers are positioned after Functions.
* [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol): State is positioned after Functions.
* [IPublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IPublicVault.sol): State is positioned after Functions.
* [IV3PositionManager.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IV3PositionManager.sol): State is positioned after Functions.
* [ICollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ICollateralToken.sol): State is positioned after Events.
* [IAstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IAstariaRouter.sol): State is positioned after Events.
* [ILienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/ILienToken.sol): State is positioned after Events.

### [NC-08] Order of Functions Not Compliant With Solidity Docs

The Solidity Style Guide suggests the following function order: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private.

The following contracts are not compliant (examples are only to prove the functions are out of order NOT a full description): 

* [BeaconProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol): fallback function is positioned after internal functions.
* [Vault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol): external functions are positioned after public functions.
* [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol): external functions are positioned after internal functions.
* [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol): external functions are positioned after public functions.
* [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol): external functions are positioned after public functions.
* [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol): public functions are positioned after internal functions.
* [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol): public functions are positioned after internal functions.
* [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol): external functions are positioned after internal functions.
* [ERC20-Cloned.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol): external functions are positioned after internal functions.
* [ERC721.sol](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol): external functions are positioned after public functions.
* [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol): external functions are positioned after internal functions.

### [NC-09] Modifiers/Functions Should Replace Duplicate `require` Statements

There are times in the codebase where the same or similar require statements are used and can be made into a single modifier for better code cleanliness. 

*/src/ClearingHouse.sol*
Links: [199](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199), [216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216).
```solidity
199:	require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
216:	require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
```

*lib/gpl/src/ERC4626-Cloned.sol*
Links: [27](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L27), [43](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L43).
```solidity
27:	require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
43:	require(assets > minDepositAmount(), "VALUE_TOO_SMALL");
```

*lib/gpl/src/ERC721.sol*
Links: [155-160](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L155-L160), [171-176](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L171-L176).
```solidity
155:	require(
156:	  to.code.length == 0 ||
157: ...
171:	require(
172:	  to.code.length == 0 ||
173: ...
```
Links: [240-250](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L240-L250), [260-270](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L260-L270).
```solidity
240:	require(
241:	  to.code.length == 0 ||
242: ...
260:	require(
261:	  to.code.length == 0 ||
262: ...
```

*/src/VaultImplementation.sol*
Links: [78](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L78), [96](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L96), [105](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L105), [144](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L114), [147](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L147), [211](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L211).
```solidity
78:	require(msg.sender == owner()); //owner is "strategist"
96:	require(msg.sender == owner()); //owner is "strategist"
105:	require(msg.sender == owner()); //owner is "strategist"
144:	require(msg.sender == owner()); //owner is "strategist"
147:	require(msg.sender == owner()); //owner is "strategist"
211:	require(msg.sender == owner()); //owner is "strategist"
```

### [NC-10] No License Indication

Some contracts are missing a license indication. If no license is used `SPDX-License-Identifier: UNLICENSED` should be at the top of a contract.

The following contracts are missing a license:

* [IWETH9.sol](https://github.com/AstariaXYZ/astaria-gpl/tree/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol)
* [IERC20Metadata.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC20Metadata.sol)
* [IERC721.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IERC721.sol)
* [IV3PositionManager.sol](https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces/IV3PositionManager.sol)

### [NC-11] `acheive` Spelling Mistake

The word `achieve` is misspelled as `acheive`.

*/src/interfaces/IV3PositionManager.sol*
Links: [122](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L122), [123](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L123).
```solidity
122:	/// @return amount0 The amount of token0 to acheive resulting liquidity
123:	/// @return amount1 The amount of token1 to acheive resulting liquidity
```

### [NC-12] `nft` vs `NFT`

`NFT` is spelled in all capitals everywhere in the codebase except 2 places (EX. [L27](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L27)). Consider changing all occurrences of `nft` to `NFT`.

*/src/ClearingHouse.sol*
Links: [115](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115).
```solidity
115:	address tokenContract, // collateral token sending the fake nft
```

*/src/interfaces/ICollateralToken.sol*
Links: [63](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L63).
```solidity
63:	//mapping of a security token hook for an nft's token contract address
```

### [NC-13] Use of `pragma experimental ABIEncoderV2;`

As of [Solidity v0.8.0](https://docs.soliditylang.org/en/v0.8.0/080-breaking-changes.html) "`pragma experimental ABIEncoderV2;` is still valid, but is deprecated and has no effect". Consider removing all occurrences of `pragma experimental ABIEncoderV2;` when contracts are >=v0.8.0.

*/src/CollateralToken.sol*
Links: [16](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L16).
```solidity
16:	pragma experimental ABIEncoderV2;
```

*/src/LienToken.sol*
Links: [16](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L16).
```solidity
16:	pragma experimental ABIEncoderV2;
```

### [NC-14] Inconsistent Code Style For Similar Code

There are two sections of code that are very similar yet written in 2 different formats `A` and `B` seen below:

*lib/gpl/src/ERC721.sol*
`A`: [155-160](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L155-L160), [171-176](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L171-L176).
```solidity
require(
  to.code.length == 0 ||
    ERC721TokenReceiver(to).onERC721Received(msg.sender, from, id, "") ==
    ERC721TokenReceiver.onERC721Received.selector,
      "UNSAFE_RECIPIENT"
);
```
`B`: [240-250](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L240-L250), [260-270](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L260-L270).
```solidity
require(
  to.code.length == 0 ||
    ERC721TokenReceiver(to).onERC721Received(
      msg.sender,
      address(0),
      id,
      ""
    ) ==
    ERC721TokenReceiver.onERC721Received.selector,
  "UNSAFE_RECIPIENT"
);
```

Style `B` will not exceed 120 characters if expanded like `A`. Consider maintaining the same code style for better readability.

### [NC-15] Extra Space in Comment

There is an extra space in the comment [L25](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L25) of [TransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L25) between `is` and `Auth`.

*/src/TransferProxy.sol*
Links: [25](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L25).
```solidity
25:	//only constructor we care about is  Auth
```

### [NC-16] Power of Ten Literal > `10e3` Not In Scientific Notation

Power of ten literals > `10e3` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation (Ex. 10e5).

*/src/PublicVault.sol*
Links: [604](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L604).
```solidity
604:	uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

### [NC-17] Duplicate Code

Duplicate code should be put into a function. [L239-L242](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L239-L242) and [L257-L260](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L257-L260) of [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol) have the same code.

### [NC-18] Empty Comment

There is an empty comment before the second `//`. Consider removing the first empty comment on [L831](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L831) of [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol).

*/src/LienToken.sol*
Links: [831](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L831).
```solidity
831:	//      // slope does not need to be updated if paying off the rest, since we neutralize slope in beforePayment()
```

### [NC-19] `require` Matches Existing Modifier

In the [`mint`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L132) function of [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol), `require(msg.sender == VAULT(), "only vault can mint");` is used which already matches the existing modifier `onlyVault`. Consider re-using `onlyVault` instead of inlining the modifier.

### [L-01] Lack of `events`

There is a general lack of `events` in the codebase especially in critical functions like a privileged actor role change (EX. [339](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339)).
