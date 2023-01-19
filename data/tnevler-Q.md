# Report
## Low Risk ##
### [L-1]: Critical changes should use two-step procedure
**Context:**

1. ```function setNewGuardian(address _guardian) external {``` [L339](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339) 

**Recommendation:**

The best practice is to use two-step procedure for critical changes to make them less error-prone. 

## Non-Critical Issues ##

### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return shares;``` [L140](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L140) 
1. ```return super.withdraw(assets, receiver, owner);``` [L174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L174) 
1. ```return super.redeem(shares, receiver, owner);``` [L195](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L195) 
1. ```return``` [L164](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L164) 
1. ```return``` [L179](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L179) 
1. ```return``` [L144](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L144) 
1. ```return timeToEpochEnd() + EPOCH_LENGTH();``` [L719](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L719) 
1. ```return super.mint(vault, to, shares, maxAmountIn);``` [L136](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L136) 
1. ```return super.deposit(vault, to, amount, minSharesOut);``` [L152](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L152) 
1. ```return super.withdraw(vault, to, amount, maxSharesOut);``` [L168](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L168) 
1. ```return super.redeem(vault, to, shares, minAmountOut);``` [L184](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L184) 
1. ```return vault.redeemFutureEpoch(shares, receiver, msg.sender, epoch);``` [L193](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L193) 
1. ```_validateCommitment(_loadRouterSlot(), commitment, timeToSecondEpochEnd);``` [L431](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L431) 
1. ```return vaultAddr;``` [L758](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L758) 
1. ```return _buyoutLien(s, params);``` [L119](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L119) 
1. ```return _getBuyout(_loadLienStorageSlot(), stack);``` [L582](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L582) 
1. ```return _makePayment(_loadLienStorageSlot(), stack, amount);``` [L605](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L605) 
1. ```return _getMaxPotentialDebtForCollateralUpToNPositions(stack, stack.length);``` [L712](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L712) 
1. ```return deposit(vault, to, amount, minSharesOut);``` [L31](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L31) 
1. ```return deposit(vault, to, amount, minSharesOut);``` [L45](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L45) 
1. ```return redeem(vault, to, amountShares, minAmountOut);``` [L58](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L58) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Variable is unused
**Context:**

1. ```function balanceOf(address account, uint256 id)``` [L82](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82) (account)
1. ```function balanceOf(address account, uint256 id)``` [L82](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82) (id)
1. ```function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)``` [L90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90) (ids)
1. ```function isApprovedForAll(address account, address operator)``` [L106](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106) (account and operator)
1. ```address tokenContract, // collateral token sending the fake nft``` [L115](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L115) 
1. ```address to, // buyer``` [L116](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L116) 
1. ```ILienToken.AuctionStack[] storage stack = s.auctionStack.stack;``` [L139](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L139) 
1. ```bytes calldata data //empty from seaport``` [L174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L174) 
1. ```address operator_,``` [L189](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L189) 
1. ```address from_,``` [L190](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L190) 
1. ```uint256 tokenId_,``` [L191](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L191) 
1. ```bytes calldata data_``` [L192](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L192) 
1. ```function deposit(uint256 assets, address receiver)``` [L143](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L143) (assets and receiver)
1. ```address tokenContract = underlying.tokenContract;``` [L138](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L138) 
1. ```uint256 tokenId = underlying.tokenId;``` [L139](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L139) 
1. ```address caller,``` [L159](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L159) 
1. ```address offerer,``` [L160](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L160) 
1. ```address caller,``` [L173](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L173) 
1. ```bytes32[] calldata priorOrderHashes,``` [L175](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L175) 
1. ```CriteriaResolver[] calldata criteriaResolvers``` [L176](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L176) 
1. ```address tokenContract = underlying.tokenContract;``` [L342](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L342) 
1. ```uint256 assets = totalAssets();``` [L262](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L262) 
1. ```function _beforeCommitToLien(IAstariaRouter.Commitment calldata params)``` [L413](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L413) (params)
1. ```function afterDeposit(uint256 assets, uint256 shares)``` [L575](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L575) (shares)
1. ```uint256 end = stack[position].end;``` [L632](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L632) 
1. ```function _getRemainingInterest(LienStorage storage s, Stack memory stack)``` [L775](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L775) (s)

### [N-3]: No same value input check
**Context:**

1. ```s.newGuardian = _guardian;``` [L342](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L342) 
1. ```s.delegate = delegate_;``` [L213](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L213) 

**Recommendation:**

Example how to fix ```require(_newOwner != owner, " Same address");```

### [N-4]: Wrong order of functions
**Context:**

1. ```function _delegate(address implementation) internal virtual {``` [L29](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L29) (internal function can not go after internal pure function)
1. ```fallback() external payable virtual {``` [L69](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L69) (fallback function can not go after internal function)
1. ```receive() external payable virtual {``` [L77](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L77) (receive function can not go after fallback function)
1. ```function deposit(uint256 amount, address receiver)``` [L59](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L59) (public function can not go after public pure function)
1. ```function setAuctionData(ILienToken.AuctionData calldata auctionData)``` [L66](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L66) (external function can not go after internal function)
1. ```uint256(keccak256("xyz.astaria.WithdrawProxy.storage.location")) - 1;``` [L50](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L50) (state variable declaration can not go after event definition)
1. ```function liquidatorNFTClaim(OrderParameters memory params) external {``` [L109](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L109) (external function can not go after public function)
1. ```modifier releaseCheck(uint256 collateralId) {``` [L253](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L253) (modifier definition can not go after internal function)
1. ```function name()``` [L76](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L76) (public view function can not go after public pure function)
1. ```modifier validVault(address targetVault) {``` [L196](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L196) (modifier definition can not go after public function)
1. ```function file(File calldata incoming) external requiresAuth {``` [L82](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L82) (external function can not go after internal function)
1. ```modifier validateStack(uint256 collateralId, Stack[] memory stack) {``` [L265](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L265) (modifier definition can not go after internal functions)
1. ```function ROUTER() external pure returns (IAstariaRouter) {``` [L26](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol#L26) (external function can not go after public function)
1. ```function COLLATERAL_TOKEN() public view returns (ICollateralToken) {``` [L62](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L62) (public view function can not go after public pure function)
1. ```function depositToVault(``` [L24](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L24) (external function can not go after public function)
1. ```function deposit(uint256 assets, address receiver)``` [L19](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L19) (public function can not go after public view function)
1. ```uint256(keccak256("xyz.astaria.VaultImplementation.storage.location")) - 1;``` [L58](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L58) (state variable declaration can not go after external function)
1. ```event FileUpdated(FileType what, bytes data);``` [L34](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L34) (Struct definition can not go after event definition)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-5]: NatSpec is missing
**Context:**

1. [TransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol)
1. [Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)
1. [ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)
1. [WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol) (16 functions are missing NatSpec)
1. [CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol) (21 functions are missing NatSpec)
1. [PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol) (45 functions are missing NatSpec)
1. [AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol) (37 functions are missing NatSpec)
1. [LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol) (41 functions are missing NatSpec)
1. [WithdrawVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawVaultBase.sol)
1. [AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol)
1. [VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol) (15 functions are missing NatSpec)
1. [ISecurityHook.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ISecurityHook.sol)
1. [IRouterBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IRouterBase.sol)
1. [ITransferProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ITransferProxy.sol)
1. [IStrategyValidator.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IStrategyValidator.sol)
1. [IAstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaVaultBase.sol)
1. [IERC721.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC721.sol)
1. [IVaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol)

### [N-6]: Typos
**Context:**

1. ```//invalid action private vautls can only be the owner or strategist``` [L90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L90) (Change **vautls** to **vaults**)
1. ```//revert auction params dont match``` [L126](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L126) (Change **dont** to **don't**)
1. ```// reset liquidationWithdrawRatio to prepare for re calcualtion``` [L307](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L307) (Change **calcualtion** to **calculation**)
1. ```* @dev Allows an on-chain or off-chain user to simulate the effects of their redeemption at the current block,``` [L230](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC4626.sol#L230) (Change **redeemption** to **redemption**)
1. ```/// @return amount0 The amount of token0 to acheive resulting liquidity``` [L122](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L122) (Change **acheive** to **achieve**)
1. ```/// @return amount1 The amount of token1 to acheive resulting liquidity``` [L123](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol#L123) (Change **acheive** to **achieve**)

### [N-7]: Line is too long
**Context:**

1. ```uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.``` [L54](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54) 
1. ```uint256 withdrawReserveReceived; // amount received from PublicVault. The WETH balance of this contract - withdrawReserveReceived = amount received from liquidations.``` [L56](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L56) 
1. ```s.withdrawReserveReceived; // will never underflow because withdrawReserveReceived is always increased by the transfer amount from the PublicVault``` [L256](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L256) 
1. ```* @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.``` [L42](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L42) 
1. ```// Blocking off payments for a lien that has exceeded the lien.end to prevent repayment unless the msg.sender() is the AuctionHouse``` [L804](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L804) 
1. ```* @param params The Commitment information containing the loan parameters and the merkle proof for the strategy supporting the requested loan.``` [L226](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L226) 
1. ```* Origination consists of a few phases: pre-commitment validation, lien token issuance, strategist reward, and after commitment actions``` [L281](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L281) 
1. ```* Starts by depositing collateral and take optimized-out a lien against it. Next, verifies the merkle proof for a loan commitment. Vault owners are then rewarded fees for successful loan origination.``` [L282](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L282) 
1. ```* @notice Retrieves the recipient of loan repayments. For PublicVaults (VAULT_TYPE 2), this is always the vault address. For PrivateVaults, retrieves the owner() of the vault.``` [L363](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L363) 
1. ```* @param c The Commitment information containing the loan parameters and the merkle proof for the strategy supporting the requested loan.``` [L376](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L376) 
1. ```* @notice Called at epoch boundary, computes the ratio between the funds of withdrawing liquidity providers and the balance of the underlying PublicVault so that claim() proportionally pays optimized-out to all parties.``` [L27](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L27) 
1. ```* @notice Adds an auction scheduled to end in a new epoch to this WithdrawProxy, to ensure that withdrawing LPs get a proportional share of auction returns.``` [L33](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L33) 
1. ```* @param finalAuctionDelta The timestamp by which the auction being added is guaranteed to end. As new auctions are added to the WithdrawProxy, this value will strictly increase as all auctions have the same maximum duration.``` [L35](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L35) 
1. ```* Returns the end timestamp of the last auction tracked by this WithdrawProxy. After this timestamp has passed, claim() can be called.``` [L74](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L74) 
1. ```The base router is a multicall style router inspired by Uniswap v3 with built-in features for permit, WETH9 wrap/unwrap, and ERC20 token pulling/sweeping/approving.``` [L10](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L10) 
1. ```NOTE the router is capable of pulling any approved token from your wallet. This is only possible when your address is msg.sender, but regardless be careful when interacting with the router or ERC4626 Vaults.``` [L15](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L15) 
1. ```/// Returns secondsPerLiquidityCumulativeX128 the seconds per in range liquidity for the life of the pool as of the observation timestamp,``` [L105](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L105) 
1. ```* @return withdrawProxyIfNearBoundary The address of the WithdrawProxy to set the payee to if the liquidation is triggered near an epoch boundary.``` [L146](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol#L146) 
1. ```* @notice Executes a FlashAction using locked collateral. A valid FlashAction performs a specified action with the collateral within a single transaction and must end with the collateral being returned to the Vault it was locked in.``` [L100](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L100) 
1. ```* @param params LienActionEncumber data containing CollateralToken information and lien parameters (rate, duration, and amount, rate, and debt caps).``` [L181](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L181) 
1. ```* @param params The LienActionBuyout data specifying the lien position, receiver address, and underlying CollateralToken information of the lien.``` [L193](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L193) 
1. ```* Calculates the debt accrued by all liens against a CollateralToken, assuming no payments are made until the end timestamp in the stack.``` [L263](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L263) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
