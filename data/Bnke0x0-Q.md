

### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::97 => s.COLLATERAL_TOKEN = _COLLATERAL_TOKEN;
2023-01-astaria/src/AstariaRouter.sol::98 => s.LIEN_TOKEN = _LIEN_TOKEN;
2023-01-astaria/src/AstariaRouter.sol::99 => s.TRANSFER_PROXY = _TRANSFER_PROXY;
2023-01-astaria/src/AstariaRouter.sol::342 => s.newGuardian = _guardian;
2023-01-astaria/src/LienToken.sol::66 => s.TRANSFER_PROXY = _TRANSFER_PROXY;
2023-01-astaria/src/astaria-gpl/src/RC721.sol::71 => s.name = _name;
2023-01-astaria/src/astaria-gpl/src/RC721.sol::72 => s.symbol = _symbol;
```



### [L02] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::83 => function initialize(
2023-01-astaria/src/AstariaRouter.sol::93 => ) external initializer {
2023-01-astaria/src/CollateralToken.sol::80 => function initialize(
2023-01-astaria/src/CollateralToken.sol::85 => ) public initializer {
2023-01-astaria/src/LienToken.sol::59 => function initialize(Authority _AUTHORITY, ITransferProxy _TRANSFER_PROXY)
2023-01-astaria/src/LienToken.sol::61 => initializer
```



###  [L03] `safeApprove()` is deprecated

#### Impact
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of safeIncreaseAllowance() and safeDecreaseAllowance()
#### Findings:
```
2023-01-astaria/src/ClearingHouse.sol::148 => ERC20(paymentToken).safeApprove(
2023-01-astaria/src/VaultImplementation.sol::334 => ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::21 => ERC20(vault.asset()).safeApprove(address(vault), shares);
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::34 => ERC20(vault.asset()).safeApprove(address(vault), amount);
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::48 => ERC20(address(vault)).safeApprove(address(vault), amount);
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::62 => ERC20(address(vault)).safeApprove(address(vault), shares);
```





### [L04] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

#### Impact
approve is subject to a known front-running attack. Consider using safeApprove instead:
#### Findings:
```
2023-01-astaria/src/ClearingHouse.sol::203 => ERC721(order.parameters.offer[0].token).approve(
```





### [L05] `pragma experimental ABIEncoderV2` is deprecated

#### Impact
Use pragma abicoder v2 [instead](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics)
#### Findings:
```
2023-01-astaria/src/CollateralToken.sol::16 => pragma experimental ABIEncoderV2;
2023-01-astaria/src/LienToken.sol::16 => pragma experimental ABIEncoderV2;
```



### [L06] `_safeMint()` should be used rather than `_mint()` wherever possible

#### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function
#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/RC721.sol::238 => _mint(to, id);
2023-01-astaria/src/astaria-gpl/src/RC721.sol::258 => _mint(to, id);
```






### [L07] Return values of transfer()/transferFrom() not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have been marked as failed may 
potentially go through without actually making a payment
#### Findings:
```
2023-01-astaria/src/ClearingHouse.sol::143 => ERC20(paymentToken).safeTransfer({
2023-01-astaria/src/ClearingHouse.sol::161 => ERC20(paymentToken).safeTransfer(
2023-01-astaria/src/PublicVault.sol::384 => ERC20(asset()).safeTransfer(currentWithdrawProxy, withdrawBalance);
2023-01-astaria/src/TransferProxy.sol::34 => ERC20(token).safeTransferFrom(from, to, amount);
2023-01-astaria/src/Vault.sol::66 => ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
2023-01-astaria/src/Vault.sol::72 => ERC20(asset()).safeTransferFrom(address(this), msg.sender, amount);
2023-01-astaria/src/VaultImplementation.sol::394 => ERC20(asset()).safeTransfer(receiver, payout);
2023-01-astaria/src/VaultImplementation.sol::406 => ERC20(asset()).safeTransfer(feeTo, fee);
2023-01-astaria/src/WithdrawProxy.sol::269 => ERC20(asset()).safeTransfer(VAULT(), balance);
2023-01-astaria/src/WithdrawProxy.sol::281 => ERC20(asset()).safeTransfer(VAULT(), balance);
2023-01-astaria/src/WithdrawProxy.sol::298 => ERC20(asset()).safeTransfer(withdrawProxy, amount);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::29 => ERC20(asset()).safeTransferFrom(msg.sender, address(this), assets);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::45 => ERC20(asset()).safeTransferFrom(msg.sender, address(this), assets);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::76 => ERC20(asset()).safeTransfer(receiver, assets);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::102 => ERC20(asset()).safeTransfer(receiver, assets);
```




### Non-Critical Issues









### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::758 => return vaultAddr;
2023-01-astaria/src/LienToken.sol::655 => return payment;
2023-01-astaria/src/Vault.sol::67 => return amount;
2023-01-astaria/src/WithdrawProxy.sol::140 => return shares;
2023-01-astaria/src/WithdrawProxy.sol::299 => return amount;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::341 => require(msg.sender == s.guardian);
2023-01-astaria/src/AstariaRouter.sol::347 => require(msg.sender == s.guardian);
2023-01-astaria/src/AstariaRouter.sol::354 => require(msg.sender == s.newGuardian);
2023-01-astaria/src/AstariaRouter.sol::361 => require(msg.sender == address(s.guardian));
2023-01-astaria/src/AstariaRouter.sol::419 => revert(0, ONE_WORD)
2023-01-astaria/src/BeaconProxy.sol::46 => revert(0, returndatasize())
2023-01-astaria/src/ClearingHouse.sol::72 => require(msg.sender == address(ASTARIA_ROUTER.LIEN_TOKEN()));
2023-01-astaria/src/ClearingHouse.sol::199 => require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
2023-01-astaria/src/ClearingHouse.sol::216 => require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
2023-01-astaria/src/ClearingHouse.sol::223 => require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
2023-01-astaria/src/CollateralToken.sol::266 => require(ownerOf(collateralId) == msg.sender);
2023-01-astaria/src/CollateralToken.sol::535 => require(msg.sender == s.clearingHouse[collateralId]);
2023-01-astaria/src/CollateralToken.sol::564 => require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
2023-01-astaria/src/CollateralToken.sol::598 => revert();
2023-01-astaria/src/LienToken.sol::860 => require(position < length);
2023-01-astaria/src/PublicVault.sol::241 => require(s.allowList[receiver]);
2023-01-astaria/src/PublicVault.sol::259 => require(s.allowList[receiver]);
2023-01-astaria/src/PublicVault.sol::508 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/PublicVault.sol::680 => require(msg.sender == address(LIEN_TOKEN()));
2023-01-astaria/src/Vault.sol::65 => require(s.allowList[msg.sender] && receiver == owner());
2023-01-astaria/src/Vault.sol::71 => require(msg.sender == owner());
2023-01-astaria/src/VaultImplementation.sol::78 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::96 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::105 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::114 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::147 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::191 => require(msg.sender == address(ROUTER()));
2023-01-astaria/src/VaultImplementation.sol::211 => require(msg.sender == owner()); //owner is "strategist"
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::67 => 0x571e08d100000000000000000000000000000000000000000000000000000000;
2023-01-astaria/src/AstariaRouter.sol::112 => s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
2023-01-astaria/src/AstariaRouter.sol::115 => s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
2023-01-astaria/src/PublicVault.sol::73 => return 18;
2023-01-astaria/src/PublicVault.sol::103 => if (ERC20(asset()).decimals() == uint8(18)) {
2023-01-astaria/src/PublicVault.sol::106 => return 10**(ERC20(asset()).decimals() - 1);
2023-01-astaria/src/PublicVault.sol::315 => .mulDivDown(1e18, totalSupply())
2023-01-astaria/src/PublicVault.sol::333 => totalAssets().mulDivDown(s.liquidationWithdrawRatio, 1e18)
2023-01-astaria/src/PublicVault.sol::604 => uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
2023-01-astaria/src/WithdrawProxy.sol::78 => return 18;
2023-01-astaria/src/WithdrawProxy.sol::260 => (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
2023-01-astaria/src/WithdrawProxy.sol::264 => (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
2023-01-astaria/src/WithdrawProxy.sol::273 => 10**ERC20(asset()).decimals()
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::132 => return supply == 0 ? 10e18 : shares.mulDivUp(totalAssets(), supply);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::140 => return supply == 0 ? 10e18 : assets.mulDivUp(supply, totalAssets());
```





### [N04] File is missing NatSpec



#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/ERC4626Router.sol::1 => // SPDX-License-Identifier: GPL-2.0-or-later
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::1 => // SPDX-License-Identifier: GPL-2.0-or-later
```




### [N05] Unused file


#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::1 => // SPDX-License-Identifier: MIT
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::1 => // SPDX-License-Identifier: MIT
2023-01-astaria/src/astaria-gpl/src/RC721.sol::1 => // SPDX-License-Identifier: MIT
```





### [N06] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::192 => ) public virtual validVault(address(vault)) returns (uint256 assets) {
2023-01-astaria/src/AstariaRouter.sol::207 => ) public payable override {
2023-01-astaria/src/AstariaRouter.sol::224 => function feeTo() public view returns (address) {
2023-01-astaria/src/AstariaRouter.sol::229 => function BEACON_PROXY_IMPLEMENTATION() public view returns (address) {
2023-01-astaria/src/AstariaRouter.sol::234 => function LIEN_TOKEN() public view returns (ILienToken) {
2023-01-astaria/src/AstariaRouter.sol::239 => function TRANSFER_PROXY() public view returns (ITransferProxy) {
2023-01-astaria/src/AstariaRouter.sol::244 => function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
2023-01-astaria/src/AstariaRouter.sol::273 => function file(File calldata incoming) public requiresAuth {
2023-01-astaria/src/AstariaRouter.sol::402 => function getAuctionWindow(bool includeBuffer) public view returns (uint256) {
2023-01-astaria/src/AstariaRouter.sol::429 => ) public view returns (ILienToken.Lien memory lien) {
2023-01-astaria/src/AstariaRouter.sol::552 => ) public whenNotPaused returns (address) {
2023-01-astaria/src/AstariaRouter.sol::680 => function isValidVault(address vault) public view returns (bool) {
2023-01-astaria/src/AstariaRouter.sol::688 => ) public view returns (bool) {
2023-01-astaria/src/AstariaVaultBase.sol::28 => function ROUTER() public pure returns (IAstariaRouter) {
2023-01-astaria/src/AstariaVaultBase.sol::32 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/AstariaVaultBase.sol::36 => function owner() public pure returns (address) {
2023-01-astaria/src/AstariaVaultBase.sol::50 => function START() public pure returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::54 => function EPOCH_LENGTH() public pure returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::58 => function VAULT_FEE() public pure returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::62 => function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
2023-01-astaria/src/ClearingHouse.sol::43 => function ROUTER() public pure returns (IAstariaRouter) {
2023-01-astaria/src/ClearingHouse.sol::47 => function COLLATERAL_ID() public pure returns (uint256) {
2023-01-astaria/src/ClearingHouse.sol::51 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/CollateralToken.sol::85 => ) public initializer {
2023-01-astaria/src/CollateralToken.sol::105 => function SEAPORT() public view returns (ConsiderationInterface) {
2023-01-astaria/src/CollateralToken.sol::206 => function file(File calldata incoming) public requiresAuth {
2023-01-astaria/src/CollateralToken.sol::370 => function getConduitKey() public view returns (bytes32) {
2023-01-astaria/src/CollateralToken.sol::375 => function getConduit() public view returns (address) {
2023-01-astaria/src/CollateralToken.sol::412 => function securityHooks(address target) public view returns (address) {
2023-01-astaria/src/CollateralToken.sol::524 => function settleAuction(uint256 collateralId) public {
2023-01-astaria/src/LienToken.sol::246 => function getInterest(Stack calldata stack) public view returns (uint256) {
2023-01-astaria/src/LienToken.sol::364 => ) public override(ERC721, IERC721) {
2023-01-astaria/src/LienToken.sol::377 => function ASTARIA_ROUTER() public view returns (IAstariaRouter) {
2023-01-astaria/src/LienToken.sol::381 => function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
2023-01-astaria/src/LienToken.sol::562 => function validateLien(Lien memory lien) public view returns (uint256 lienId) {
2023-01-astaria/src/LienToken.sol::702 => function calculateSlope(Stack memory stack) public pure returns (uint256) {
2023-01-astaria/src/LienToken.sol::893 => function getPayee(uint256 lienId) public view returns (address) {
2023-01-astaria/src/PublicVault.sol::121 => ) public virtual override(ERC4626Cloned) returns (uint256 assets) {
2023-01-astaria/src/PublicVault.sol::130 => ) public virtual override(ERC4626Cloned) returns (uint256 shares) {
2023-01-astaria/src/PublicVault.sol::143 => ) public virtual returns (uint256 assets) {
2023-01-astaria/src/PublicVault.sol::192 => function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {
2023-01-astaria/src/PublicVault.sol::196 => function getCurrentEpoch() public view returns (uint64) {
2023-01-astaria/src/PublicVault.sol::200 => function getSlope() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::204 => function getWithdrawReserve() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::208 => function getLiquidationWithdrawRatio() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::212 => function getYIntercept() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::275 => function processEpoch() public {
2023-01-astaria/src/PublicVault.sol::359 => function transferWithdrawReserve() public {
2023-01-astaria/src/PublicVault.sol::461 => function accrue() public returns (uint256) {
2023-01-astaria/src/PublicVault.sol::534 => function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::546 => function getLienEpoch(uint64 end) public pure returns (uint64) {
2023-01-astaria/src/PublicVault.sol::552 => function getEpochEnd(uint256 epoch) public pure returns (uint64) {
2023-01-astaria/src/PublicVault.sol::562 => function afterPayment(uint256 computedSlope) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::611 => function LIEN_TOKEN() public view returns (ILienToken) {
2023-01-astaria/src/PublicVault.sol::643 => ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
2023-01-astaria/src/PublicVault.sol::669 => function increaseYIntercept(uint256 amount) public {
2023-01-astaria/src/PublicVault.sol::684 => function decreaseYIntercept(uint256 amount) public {
2023-01-astaria/src/PublicVault.sol::699 => function timeToEpochEnd() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::703 => function timeToEpochEnd(uint256 epoch) public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::722 => function timeToSecondEpochEnd() public view returns (uint256) {
2023-01-astaria/src/VaultImplementation.sol::152 => function domainSeparator() public view virtual returns (bytes32) {
2023-01-astaria/src/VaultImplementation.sol::366 => function recipient() public view returns (address) {
2023-01-astaria/src/WithdrawProxy.sol::77 => function decimals() public pure override returns (uint8) {
2023-01-astaria/src/WithdrawProxy.sol::215 => function getFinalAuctionEnd() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::220 => function getWithdrawRatio() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::225 => function getExpected() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::240 => function claim() public {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
2023-01-astaria/src/WithdrawProxy.sol::309 => ) public onlyVault {
2023-01-astaria/src/WithdrawVaultBase.sol::22 => function name() public view virtual returns (string memory);
2023-01-astaria/src/WithdrawVaultBase.sol::24 => function symbol() public view virtual returns (string memory);
2023-01-astaria/src/WithdrawVaultBase.sol::30 => function IMPL_TYPE() public pure override(IRouterBase) returns (uint8) {
2023-01-astaria/src/WithdrawVaultBase.sol::34 => function asset() public pure virtual override(IERC4626) returns (address) {
2023-01-astaria/src/WithdrawVaultBase.sol::38 => function VAULT() public pure returns (address) {
2023-01-astaria/src/WithdrawVaultBase.sol::42 => function CLAIMABLE_EPOCH() public pure returns (uint64) {
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::52 => function transfer(address to, uint256 amount) public virtual returns (bool) {
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::76 => function totalSupply() public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::85 => ) public virtual returns (bool) {
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::114 => ) public virtual {
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::154 => function DOMAIN_SEPARATOR() public view virtual returns (bytes32) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::15 => function minDepositAmount() public view virtual returns (uint256);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::17 => function asset() public view virtual returns (address);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::41 => ) public virtual returns (uint256 assets) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::58 => ) public virtual returns (uint256 shares) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::83 => ) public virtual returns (uint256 assets) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::105 => function totalAssets() public view virtual returns (uint256);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::109 => ) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::117 => ) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::125 => ) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::129 => function previewMint(uint256 shares) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::137 => ) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::143 => function previewRedeem(uint256 shares) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::147 => function maxDeposit(address) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::151 => function maxMint(address) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::155 => function maxWithdraw(address owner) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::160 => function maxRedeem(address owner) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/ERC4626Router.sol::39 => ) public payable override returns (uint256 sharesOut) {
2023-01-astaria/src/astaria-gpl/src/ERC4626Router.sol::53 => ) public payable override returns (uint256 amountOut) {
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::20 => ) public payable virtual override returns (uint256 amountIn) {
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::33 => ) public payable virtual override returns (uint256 sharesOut) {
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::46 => ) public payable virtual override returns (uint256 sharesOut) {
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::60 => ) public payable virtual override returns (uint256 amountOut) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::26 => function getApproved(uint256 tokenId) public view returns (address) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::52 => function ownerOf(uint256 id) public view virtual returns (address owner) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::59 => function balanceOf(address owner) public view virtual returns (uint256) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::79 => function name() public view returns (string memory) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::83 => function symbol() public view returns (string memory) {
2023-01-astaria/src/astaria-gpl/src/RC721.sol::110 => ) public virtual override(IERC721) {
```



### [N07] Numeric values having to do with time should use time units for readability

#### Impact
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::107 => s.auctionWindow = uint32(2 days);
2023-01-astaria/src/AstariaRouter.sol::108 => s.auctionWindowBuffer = uint32(1 days);
2023-01-astaria/src/AstariaRouter.sol::112 => s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
2023-01-astaria/src/AstariaRouter.sol::113 => s.minEpochLength = uint32(7 days);
2023-01-astaria/src/AstariaRouter.sol::114 => s.maxEpochLength = uint32(45 days);
2023-01-astaria/src/AstariaRouter.sol::115 => s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
2023-01-astaria/src/AstariaRouter.sol::116 => //63419583966; // 200% apy / second
2023-01-astaria/src/AstariaRouter.sol::119 => s.minDurationIncrease = uint32(5 days);
```



### [N08] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/ERC4626Router.sol::2 => pragma solidity ^0.8.17;
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::2 => pragma solidity ^0.8.17;
2023-01-astaria/src/astaria-gpl/src/RC721.sol::2 => pragma solidity ^0.8.16;
```






