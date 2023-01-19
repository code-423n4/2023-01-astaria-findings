# QA Report for Astaria contest
## Overview
During the audit, 1 low and 12 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
L-1 | Use the two-step-transfer of Guardian | Low | 1
NC-1 | Order of Functions | Non-Critical | 22
NC-2 | Order of Layout | Non-Critical | 9
NC-3 | Unused variable | Non-Critical | 22
NC-4 | Typos | Non-Critical | 5
NC-5 | Function is not implemented | Non-Critical | 1
NC-6 | Missing leading underscores | Non-Critical | 15
NC-7 | Empty function bodies | Non-Critical | 2
NC-8 | Missing NatSpec | Non-Critical | 10
NC-9 | Visibility is not marked | Non-Critical | 4
NC-10 | Unused named return variables | Non-Critical | 21
NC-11 | Maximum line length exceeded | Non-Critical | 22
NC-12 | Inconsistency when using the number 1000 | Non-Critical | 1

## Low Risk Findings(1)
### L-1. Use the two-step-transfer of Guardian
##### Description
If the guardian accidentally transfers ownership to an incorrect address, protected functions may become permanently inaccessible.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L342

##### Recommendation
Consider using a two-step-transfer of ownership: the current owner would nominate a new owner, and to become the new owner, the nominated account would have to approve the change, so that the address is proven to be valid.

## Non-Critical Risk Findings(12)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
fallback() should be placed right after constructor and receive() function:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L69

receive() should be placed right after constructor:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L77

external functions should not go after internal functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L66-L112
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L157-L183
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339-L400
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L82

public functions should not go after internal functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L169-L186
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L370-L414

external functions should not go after public functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L188-L231
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L109
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L196
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L252-L271
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L522
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L577
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L650-L678
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L26
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L24

internal functions should not go between public functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L217
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L277
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L407
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L434

public function should not go between external functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L96

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
state variable should not go after events:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L50

state variable should not go between functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L58

event should be placed after all structs:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L34

modifiers should be placed between state variables/events and functions:
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L152
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L230
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L253
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L265
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L196
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L265

#
### NC-3. Unused variable
##### Description
Some variables are not used in the functions code.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82 (account, id)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90 (ids)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106 (account, operator)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L116 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L189 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L190
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L191 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L192
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L138
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L139 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L159
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L160
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L173
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L175
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L176 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L342
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L262
- (https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L413 (params)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L575 (shares)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L632 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L775 (s)

##### Recommendation
Delete unused variables or use them.
#
### NC-4. Typos
##### Instances
- [```//invalid action private vautls can only be the owner or strategist```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L90) => ```vaults```
- [```// reset liquidationWithdrawRatio to prepare for re calcualtion```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L307) => ```calculation```
- [```* @dev Allows an on-chain or off-chain user to simulate the effects of their redeemption at the current block,```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC4626.sol#L230) => ```redemption```
- [```/// @return amount0 The amount of token0 to acheive resulting liquidity```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L122) => ```achieve```
- [```/// @return amount1 The amount of token1 to acheive resulting liquidity```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L123) => ```achieve```

#
### NC-5. Function is not implemented
##### Description
Function ```balanceOf``` always returns incorrect result.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82 

#
### NC-6. Missing leading underscores
##### Description
It is a good practice when private constants have a leading underscore.
##### Instances
- [```uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L40) 
- [```uint256 private constant WITHDRAW_PROXY_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L49) 
- [```uint256 private constant COLLATERAL_TOKEN_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73) 
- [```uint256 private constant PUBLIC_VAULT_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L53) 
- [```uint256 private constant ROUTER_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L62) 
- [```uint256 private constant OUTOFBOUND_ERROR_SELECTOR =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L66) 
- [```uint256 private constant ONE_WORD = 0x20;```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L68) 
- [```uint256 private constant LIEN_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L50) 
- [```bytes32 constant ACTIVE_AUCTION = bytes32("ACTIVE_AUCTION");```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L53) 
- [```uint256 constant ERC20_SLOT =```](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L13) 
- [```bytes32 private constant PERMIT_TYPEHASH =```](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L15) 
- [```uint256 private constant ERC721_SLOT =```](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L15) 
- [```bytes32 constant EIP_DOMAIN =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L47) 
- [```bytes32 constant VERSION = keccak256("0");```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L51) 
- [```uint256 private constant VI_SLOT =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L57) 

##### Recommendation
Consider adding leading underscores.
#
### NC-7. Empty function bodies
##### Description
Some functions do not have implementation.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L186

##### Recommendation
Consider marking these functions as not implemented or removing them.
#
### NC-8. Missing NatSpec
##### Description
NatSpec is missing in 10 contracts.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ISecurityHook.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IRouterBase.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ITransferProxy.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IStrategyValidator.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC721.sol
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol

##### Recommendation
Add NatSpec for all functions.
#
### NC-9. Visibility is not marked
##### Description
It is a good practice to explicitly mark visibility.
##### Instances
- [```bytes32 constant ACTIVE_AUCTION = bytes32("ACTIVE_AUCTION");```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L53)
- [```uint256 constant ERC20_SLOT =```](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L13) 
- [```bytes32 constant EIP_DOMAIN =```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L47) 
- [```bytes32 constant VERSION = keccak256("0");```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L51) 

#
### NC-10. Unused named return variables
##### Description
Both named return variable(s) and return statement are used.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L140
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L174
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L195 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L164
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L179
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L144
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L719
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L136 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L152
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L168
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L184
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L193
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L431
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L758
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L119
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L582
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L605
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L712
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L31
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L45
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L58

##### Recommendation
To improve clarity use only named return variables.  
For example, change:
```
function functionName() returns (uint id) {
    return x;
```
to
```
function functionName() returns (uint id) {
    id = x;
```
#
### NC-11. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.  Longer lines make the code harder to read.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L56 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L256
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L42
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L804
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L226
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L281
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L282 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L363
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L376
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L27
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L33
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L35
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L74
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L10
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L15
- https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L105
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L146
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L100
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L181
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L193
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L263

##### Recommendation
Make the lines shorter.
#
### NC-12. Inconsistency when using the number 1000
##### Description
In some cases, 1000 is used, and in some - 1_000.
##### Instances
1_000:
- [```endingPrice: 1_000 wei```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L645)

1000/10000:
- [```uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L604) 
- [```s.liquidationFeeDenominator = uint32(1000);```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L111) 
- [```s.buyoutFeeDenominator = uint32(1000);```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L118) 
- [```auctionData.endAmount = uint88(1000 wei);```](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L342) 

##### Recommendation
Stick to one style.
#