## Summary	
### Low risk findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Outdated Open Zeppelin versions|5|
|| Total|5|

### Non-critical findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Constant value definitions that include call to `keccak256` should use `immutable`|13|
|2| Remove references to `pragma experimental ABIEncoderV2`|2|
|3| Some comments could be converted to Natspec statements|3 + multiple|
|4| Long single-line comments|32|
|5| Incorrect comment syntax|1|
|6| Update sensitive term|1|
|7| Typos|6|
|| Total|58 + multiple|
___
### Low risk findings

| No. | Description | 
|--| ----------- | 
|1|**Outdated Open Zeppelin versions**|
|  |The versions used have known vulnerabilties, all of which have been patched by version `4.7.3`. See [Security Advisories](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).|
___
[IERC165.sol: L2](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC165.sol#L2)
```solidity
// OpenZeppelin Contracts v4.4.1 (utils/introspection/IERC165.sol)
```
___
[IERC721Receiver.sol: L2](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC721Receiver.sol#L2)
```solidity
// OpenZeppelin Contracts (last updated v4.6.0) (token/ERC721/IERC721Receiver.sol)
```
___
[IERC1155Receiver.sol: L2](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC1155Receiver.sol#L2)
```solidity
// OpenZeppelin Contracts (last updated v4.5.0) (token/ERC1155/IERC1155Receiver.sol)
```
___
[IERC20.sol: L2](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC20.sol#L2)
```solidity
// OpenZeppelin Contracts (last updated v4.6.0) (token/ERC20/IERC20.sol)
```
___
[IERC1155.sol: L2](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IERC1155.sol#L2)
```solidity
// OpenZeppelin Contracts v4.4.1 (token/ERC1155/IERC1155.sol)
```
___
___


### Non-critical findings

| No. | Description |
|--| ----------- | 
|1|Constant value definitions that include call to `keccak256` should use `immutable`|
|  |Constant value definitions including a call to `keccak256` should use `immutable` instead of `constant`|
___
[ClearingHouse.sol: L40-41](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L40-L41)
```solidity
  uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =
    uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
```
___
[WithdrawProxy.sol: L49-50](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L49-L50)
```solidity
  uint256 private constant WITHDRAW_PROXY_SLOT =
    uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;
```
___
[CollateralToken.sol: L73-74](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L73-L74)
```solidity
  uint256 private constant COLLATERAL_TOKEN_SLOT =
    uint256(keccak256("xyz.astaria.CollateralToken.storage.location")) - 1;
```
___
[PublicVault.sol: L53-54](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L53-L54)
```solidity
  uint256 private constant PUBLIC_VAULT_SLOT =
    uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
```
___
[AstariaRouter.sol: L62-63](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L62-L63)
```solidity
  uint256 private constant PUBLIC_VAULT_SLOT =
    uint256(keccak256("xyz.astaria.PublicVault.storage.location")) - 1;
```
___
[LienToken.sol: L50-51](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L50-L51)
```solidity
  uint256 private constant LIEN_SLOT =
    uint256(keccak256("xyz.astaria.LienToken.storage.location")) - 1;
```
___
[ERC20-Cloned.sol: L13-14](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L13-L14)
```solidity
  uint256 constant ERC20_SLOT =
    uint256(keccak256("xyz.astaria.ERC20.storage.location")) - 1;
```
___
[ERC20-Cloned.sol: L15-18](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L15-L18)
```solidity
  bytes32 private constant PERMIT_TYPEHASH =
    keccak256(
      "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
    );
```
___
[ERC721.sol: L15-16](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L15-L16)
```solidity
  uint256 private constant ERC721_SLOT =
    uint256(keccak256("xyz.astaria.ERC721.storage.location")) - 1;
```
___
[VaultImplementation.sol: L44-45](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L44-L45)
```solidity
  bytes32 public constant STRATEGY_TYPEHASH =
    keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");
```
___
[VaultImplementation.sol: L47-50](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L47-L50)
```solidity
  bytes32 constant EIP_DOMAIN =
    keccak256(
      "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
    );
```
___
[VaultImplementation.sol: L51](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L51)
```solidity
  bytes32 constant VERSION = keccak256("0");
```
___
[VaultImplementation.sol: L57-58](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L57-L58)
```solidity
  uint256 private constant VI_SLOT =
    uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;
```
___


| No. | Description | 
|--| ------------------------------------------------------------------------------------------- | 
|2|**Remove references to `pragma experimental ABIEncoderV2`**|
||Remove references to `pragma experimental ABIEncoderV2`, which has been depreciated. As of Solidity v0.8.0, ABI coder v2 is it is activated by default by the compiler.|

___
[CollateralToken.sol: L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L16)
```solidity
pragma experimental ABIEncoderV2;
```
___
[LienToken.sol: L16](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L16)
```solidity
pragma experimental ABIEncoderV2;
```
___


| No. | Description |
|--| ----------- | 
|3|Some comments could be converted to Natspec statements|
|  |Some comment lines that explain the purpose of `functions` could appropriately be converted into Natspec statements, in particular, `@notice` or `@dev`. Below are just a few examples:|	

___
[IWithdrawProxy.sol: L74](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L74)
```solidity
   * Returns the end timestamp of the last auction tracked by this WithdrawProxy. After this timestamp has passed, claim() can be called.
```
Suggestion:
```solidity
   * @notice Returns the end timestamp of the last auction tracked by this WithdrawProxy.
   *   After this timestamp has passed, claim() can be called.
```
Note that I have wrapped the `@notice` statement above and `@dev` statement below since they exceed 120 characters
___
[IPublicVault.sol: L79](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IPublicVault.sol#L79)
```solidity
   * The rate for the LienToken is subtracted from the total slope of the PublicVault, and recalculated in afterPayment().
```
Suggestion:
```solidity
   * @dev The rate for the LienToken is subtracted from the total slope of
   *   the PublicVault, and recalculated in afterPayment().
```
___
[ILienToken.sol: L272](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L272)
```solidity
   * Calculates the debt accrued by all liens against a CollateralToken, assuming no payments are made until the provided timestamp.
```
Suggestion:
```solidity
   * @notice Calculates the debt accrued by all liens against a CollateralToken, 
   *   assuming no payments are made until the provided timestamp.
```
___

| No. | Description |
|--| ----------- | 
|4|Long single-line comments|
|  |In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are becoming acceptable and a scroll bar is provided, very long comments can interfere with readability.| 
|  |Many of the long comments in Astoria do wrap. However, below are instances of extra-long comments (over 120 characters) whose readability could be improved by wrapping, as shown.|
|  |Note that a small indentation is used in each continuation line (which flags for the reader that the comment is ongoing), along with a period at the close (to signal the end of the comment). In addition, if a line containing both code and a comment is longer than 120 characters, it makes sense to put the comment in the line above.|

___
[LienToken.sol: L42](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L42)
```solidity
 * @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.
```
Suggestion:
```solidity
 * @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized  
 *   NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are
 *   issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.
```
___
[VaultImplementation.sol: L226](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L226)
```solidity
   * @param params The Commitment information containing the loan parameters and the merkle proof for the strategy supporting the requested loan.
```
Suggestion:
```solidity
 * @param params The Commitment information containing the loan parameters and  
 *   the merkle proof for the strategy supporting the requested loan.
```
___
[IPublicVault.sol: L146](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IPublicVault.sol#L146)
```solidity
   * @return withdrawProxyIfNearBoundary The address of the WithdrawProxy to set the payee to if the liquidation is triggered near an epoch boundary.
```
Suggestion:
```solidity
 * @return withdrawProxyIfNearBoundary The address of the WithdrawProxy to set  
 *   the payee to if the liquidation is triggered near an epoch boundary.
```
___

Similarly for the following extra-long lines:

[WithdrawProxy.sol: L54](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L54)

[WithdrawProxy.sol: L56](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L54)

[WithdrawProxy.sol: L256](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L256)

[CollateralToken.sol: L349](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L349)

[PublicVault.sol: L476](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L476)

[AstariaRouter.sol: L75](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L75)

[LienToken.sol: L804](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L804)

[VaultImplementation.sol: L363](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L363)

[VaultImplementation.sol: L376](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L376)

[IWithdrawProxy.sol: L27](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L27)

[IWithdrawProxy.sol: L33](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L33)

[IWithdrawProxy.sol: L35](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L35)

[IWithdrawProxy.sol: L52](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L52)

[IWithdrawProxy.sol: L64](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IWithdrawProxy.sol#L64)

[IUniswapV3PoolState.sol: L34](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L34)

[IUniswapV3PoolState.sol: L38](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L38)

[IUniswapV3PoolState.sol: L58](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L58)

[IUniswapV3PoolState.sol: L60](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L60)

[IUniswapV3PoolState.sol: L104](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L104)

[IUniswapV3PoolState.sol: L105](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L105)

[IERC4626RouterBase.sol: L10](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L10)

[IERC4626RouterBase.sol: L15](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L15)

[IPublicVault.sol: L145](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IPublicVault.sol#L145)

[ICollateralToken.sol: L100](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ICollateralToken.sol#L100)

[ICollateralToken.sol: L161](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ICollateralToken.sol#L161)

[IAstariaRouter.sol: L224](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L224)

[IAstariaRouter.sol: L232](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L232)

[ILienToken.sol: L181](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L181)

[ILienToken.sol: L193](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L193)
___

| No. | Description |
|--| ----------- | 
|5|Incorrect comment syntax|

___
[LienToken: L831](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L831)
```solidity
      //      // slope does not need to be updated if paying off the rest, since we neutralize slope in beforePayment()
```
Change to:
```solidity
      // slope does not need to be updated if paying off the rest, since we neutralize slope in beforePayment()
```
___

| No. | Description |
|--| ----------- | 
|6|Update sensitive term|
|  |Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/) and this practice appears to have been adopted in Astaria. In fact, `allowlist` and its variants are used throughout instead of `whitelist`, with the exception of the comment below, which appears to be an inadvertant leftover.|

___
[AstariaRouter.sol: L709](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L709)
```solidity
   * @param allowListEnabled Whether or not the Vault has an LP whitelist.
```
Suggestion: Change `whitelist` to `allowList` 
___

| No. | Description |
|--| ----------- | 
|7|Typos|

___
[Vault.sol: L90](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L90)
```solidity
    //invalid action private vautls can only be the owner or strategist
```
Change `vautls` to `vaults`
___
[PublicVault.sol: L307](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L307)
```solidity
    // reset liquidationWithdrawRatio to prepare for re calcualtion
```
Change `re calcualtion` to `recalculation`
___
[PublicVault.sol: L374](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L374)
```solidity
      // prevent transfer of more assets then are available
```
Change `then` to `than`
___
[LienToken.sol: L315](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L315)
```solidity
        // update the public vault state and get the liquidation accountant back if any
```
Change `accountant` to `amount`, if that was what was intended
___
The same typo (`acheive`) occurs in both lines below:

[IV3PositionManager.sol: L122-123](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IV3PositionManager.sol#L122-L123)
```solidity
  /// @return amount0 The amount of token0 to acheive resulting liquidity
  /// @return amount1 The amount of token1 to acheive resulting liquidity
```
Change `acheive` to `achieve` in both cases
___
___