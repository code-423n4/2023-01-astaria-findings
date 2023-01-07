

### [G01] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::265 => for (; i < files.length; ) {
2023-01-astaria/src/AstariaRouter.sol::364 => for (; i < file.length; ) {
2023-01-astaria/src/AstariaRouter.sol::506 => for (; i < commitments.length; ) {
2023-01-astaria/src/ClearingHouse.sol::96 => for (uint256 i; i < accounts.length; ) {
2023-01-astaria/src/CollateralToken.sol::198 => for (; i < files.length; ) {
2023-01-astaria/src/LienToken.sol::153 => for (uint256 i = params.encumber.stack.length; i > 0; ) {
2023-01-astaria/src/LienToken.sol::304 => for (; i < stack.length; ) {
2023-01-astaria/src/LienToken.sol::472 => for (uint256 i = stack.length; i > 0; ) {
2023-01-astaria/src/LienToken.sol::520 => for (; i < stack.length;) {
2023-01-astaria/src/LienToken.sol::669 => for (uint256 i; i < newStack.length; ) {
2023-01-astaria/src/LienToken.sol::734 => for (; i < stack.length; ) {
2023-01-astaria/src/VaultImplementation.sol::201 => for (; i < params.allowList.length; ) {
```






### [G02] Not using the named return variables when a function returns, wastes deployment gas



#### Findings:
```
2023-01-astaria/src/AstariaVaultBase.sol::33 => return _getArgUint8(20); //ends at 21
2023-01-astaria/src/AstariaVaultBase.sol::37 => return _getArgAddress(21); //ends at 44
2023-01-astaria/src/AstariaVaultBase.sol::47 => return _getArgAddress(41); //ends at 64
2023-01-astaria/src/AstariaVaultBase.sol::51 => return _getArgUint256(61);
2023-01-astaria/src/AstariaVaultBase.sol::55 => return _getArgUint256(93); //ends at 116
2023-01-astaria/src/AstariaVaultBase.sol::59 => return _getArgUint256(125);
2023-01-astaria/src/ClearingHouse.sol::48 => return _getArgUint256(21);
2023-01-astaria/src/ClearingHouse.sol::52 => return _getArgUint8(20);
2023-01-astaria/src/LienToken.sol::119 => return _buyoutLien(s, params);
2023-01-astaria/src/LienToken.sol::247 => return _getInterest(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::582 => return _getBuyout(_loadLienStorageSlot(), stack);
2023-01-astaria/src/LienToken.sol::605 => return _makePayment(_loadLienStorageSlot(), stack, amount);
2023-01-astaria/src/LienToken.sol::712 => return _getMaxPotentialDebtForCollateralUpToNPositions(stack, stack.length);
2023-01-astaria/src/LienToken.sol::744 => return _getOwed(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::753 => return _getOwed(stack, timestamp);
2023-01-astaria/src/LienToken.sol::897 => return _getPayee(_loadLienStorageSlot(), lienId);
2023-01-astaria/src/PublicVault.sol::462 => return _accrue(_loadStorageSlot());
2023-01-astaria/src/PublicVault.sol::487 => return _totalAssets(s);
2023-01-astaria/src/PublicVault.sol::723 => return _timeToSecondEndIfPublic();
2023-01-astaria/src/VaultImplementation.sol::175 => return _encodeStrategyData(s, strategy, root);
2023-01-astaria/src/WithdrawVaultBase.sol::31 => return _getArgUint8(20);
2023-01-astaria/src/WithdrawVaultBase.sol::35 => return _getArgAddress(21);
2023-01-astaria/src/WithdrawVaultBase.sol::39 => return _getArgAddress(41);
2023-01-astaria/src/WithdrawVaultBase.sol::43 => return _getArgUint64(61);
```








### [G03] Splitting `require()` statements that use `&&` Cost gas


#### Findings:
```
2023-01-astaria/src/Vault.sol::65 => require(s.allowList[msg.sender] && receiver == owner());
```



### [G04] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::100 => s.implementations[uint8(ImplementationType.PrivateVault)] = _SOLO_IMPL;
2023-01-astaria/src/AstariaRouter.sol::101 => s.implementations[uint8(ImplementationType.PublicVault)] = _VAULT_IMPL;
2023-01-astaria/src/AstariaRouter.sol::102 => s.implementations[uint8(ImplementationType.WithdrawProxy)] = _WITHDRAW_IMPL;
2023-01-astaria/src/AstariaRouter.sol::104 => uint8(ImplementationType.ClearingHouse)
2023-01-astaria/src/AstariaRouter.sol::107 => s.auctionWindow = uint32(2 days);
2023-01-astaria/src/AstariaRouter.sol::108 => s.auctionWindowBuffer = uint32(1 days);
2023-01-astaria/src/AstariaRouter.sol::110 => s.liquidationFeeNumerator = uint32(130);
2023-01-astaria/src/AstariaRouter.sol::111 => s.liquidationFeeDenominator = uint32(1000);
2023-01-astaria/src/AstariaRouter.sol::113 => s.minEpochLength = uint32(7 days);
2023-01-astaria/src/AstariaRouter.sol::114 => s.maxEpochLength = uint32(45 days);
2023-01-astaria/src/AstariaRouter.sol::117 => s.buyoutFeeNumerator = uint32(100);
2023-01-astaria/src/AstariaRouter.sol::118 => s.buyoutFeeDenominator = uint32(1000);
2023-01-astaria/src/AstariaRouter.sol::119 => s.minDurationIncrease = uint32(5 days);
2023-01-astaria/src/AstariaRouter.sol::191 => uint64 epoch
2023-01-astaria/src/AstariaRouter.sol::329 => (uint8 TYPE, address addr) = abi.decode(data, (uint8, address));
2023-01-astaria/src/AstariaRouter.sol::368 => (uint8 implType, address addr) = abi.decode(data, (uint8, address));
2023-01-astaria/src/AstariaRouter.sol::395 => function getImpl(uint8 implType) external view returns (address impl) {
2023-01-astaria/src/AstariaRouter.sol::442 => uint8 nlrType = uint8(_sliceUint(commitment.lienRequest.nlrDetails, 0));
2023-01-astaria/src/AstariaRouter.sol::621 => function liquidate(ILienToken.Stack[] memory stack, uint8 position)
2023-01-astaria/src/AstariaRouter.sol::686 => uint8 position,
2023-01-astaria/src/AstariaRouter.sol::722 => uint8 vaultType;
2023-01-astaria/src/AstariaRouter.sol::725 => vaultType = uint8(ImplementationType.PublicVault);
2023-01-astaria/src/AstariaRouter.sol::727 => vaultType = uint8(ImplementationType.PrivateVault);
2023-01-astaria/src/AstariaVaultBase.sol::32 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/ClearingHouse.sol::51 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/CollateralToken.sol::571 => uint8(IAstariaRouter.ImplementationType.ClearingHouse),
2023-01-astaria/src/LienToken.sol::67 => s.maxLiens = uint8(5);
2023-01-astaria/src/LienToken.sol::309 => uint88 owed = _getOwed(stack[i], block.timestamp);
2023-01-astaria/src/LienToken.sol::342 => auctionData.endAmount = uint88(1000 wei);
2023-01-astaria/src/LienToken.sol::412 => uint8(params.stack.length),
2023-01-astaria/src/LienToken.sol::418 => uint8(params.stack.length),
2023-01-astaria/src/LienToken.sol::420 => uint8(newStack.length)
2023-01-astaria/src/LienToken.sol::611 => uint8 position,
2023-01-astaria/src/LienToken.sol::675 => uint8(i),
2023-01-astaria/src/LienToken.sol::742 => function getOwed(Stack memory stack) external view returns (uint88) {
2023-01-astaria/src/LienToken.sol::750 => returns (uint88)
2023-01-astaria/src/LienToken.sol::764 => returns (uint88)
2023-01-astaria/src/LienToken.sol::793 => uint8 position,
2023-01-astaria/src/LienToken.sol::803 => uint64 end = stack.point.end;
2023-01-astaria/src/LienToken.sol::855 => function _removeStackPosition(Stack[] memory stack, uint8 position)
2023-01-astaria/src/LienToken.sol::879 => uint8(newStack.length)
2023-01-astaria/src/PublicVault.sol::71 => returns (uint8)
2023-01-astaria/src/PublicVault.sol::103 => if (ERC20(asset()).decimals() == uint8(18)) {
2023-01-astaria/src/PublicVault.sol::142 => uint64 epoch
2023-01-astaria/src/PublicVault.sol::153 => uint64 epoch
2023-01-astaria/src/PublicVault.sol::192 => function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {
2023-01-astaria/src/PublicVault.sol::196 => function getCurrentEpoch() public view returns (uint64) {
2023-01-astaria/src/PublicVault.sol::216 => function _deployWithdrawProxyIfNotDeployed(VaultData storage s, uint64 epoch)
2023-01-astaria/src/PublicVault.sol::224 => uint8(IAstariaRouter.ImplementationType.WithdrawProxy),
2023-01-astaria/src/PublicVault.sol::362 => if (s.currentEpoch == uint64(0)) {
2023-01-astaria/src/PublicVault.sol::440 => uint40 lienEnd,
2023-01-astaria/src/PublicVault.sol::449 => uint48 newSlope = s.slope + lienSlope.safeCastTo48();
2023-01-astaria/src/PublicVault.sol::453 => uint64 epoch = getLienEpoch(lienEnd);
2023-01-astaria/src/PublicVault.sol::459 => event SlopeUpdated(uint48 newSlope);
2023-01-astaria/src/PublicVault.sol::523 => uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
2023-01-astaria/src/PublicVault.sol::529 => function _setSlope(VaultData storage s, uint48 newSlope) internal {
2023-01-astaria/src/PublicVault.sol::534 => function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::538 => function _decreaseEpochLienCount(VaultData storage s, uint64 epoch) internal {
2023-01-astaria/src/PublicVault.sol::546 => function getLienEpoch(uint64 end) public pure returns (uint64) {
2023-01-astaria/src/PublicVault.sol::556 => function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
2023-01-astaria/src/PublicVault.sol::605 => uint88 feeInShares = convertToShares(fee).safeCastTo88();
2023-01-astaria/src/PublicVault.sol::622 => uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
2023-01-astaria/src/PublicVault.sol::654 => uint64 lienEpoch = getLienEpoch(params.lienEnd);
2023-01-astaria/src/PublicVault.sol::671 => uint64 currentEpoch = s.currentEpoch;
2023-01-astaria/src/PublicVault.sol::686 => uint64 currentEpoch = s.currentEpoch;
2023-01-astaria/src/VaultImplementation.sol::269 => uint40 end,
2023-01-astaria/src/VaultImplementation.sol::315 => uint8 position,
2023-01-astaria/src/VaultImplementation.sol::367 => if (IMPL_TYPE() == uint8(IAstariaRouter.ImplementationType.PublicVault)) {
2023-01-astaria/src/WithdrawProxy.sol::53 => uint88 withdrawRatio;
2023-01-astaria/src/WithdrawProxy.sol::54 => uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.
2023-01-astaria/src/WithdrawProxy.sol::55 => uint40 finalAuctionEnd; // when this is deleted, we know the final auction is over
2023-01-astaria/src/WithdrawProxy.sol::77 => function decimals() public pure override returns (uint8) {
2023-01-astaria/src/WithdrawProxy.sol::314 => uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();
2023-01-astaria/src/WithdrawVaultBase.sol::30 => function IMPL_TYPE() public pure override(IRouterBase) returns (uint8) {
```



### [G05] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function



#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::341 => require(msg.sender == s.guardian);
2023-01-astaria/src/ClearingHouse.sol::223 => require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
2023-01-astaria/src/CollateralToken.sol::121 => revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
2023-01-astaria/src/CollateralToken.sol::134 => revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
2023-01-astaria/src/LienToken.sol::113 => revert InvalidState(InvalidStates.EXPIRED_LIEN);
2023-01-astaria/src/LienToken.sol::141 => revert InvalidState(InvalidStates.COLLATERAL_AUCTION);
2023-01-astaria/src/LienToken.sol::214 => revert InvalidState(InvalidStates.DEBT_LIMIT);
2023-01-astaria/src/LienToken.sol::169 => revert InvalidState(InvalidStates.INITIAL_ASK_EXCEEDED);
2023-01-astaria/src/LienToken.sol::565 => revert InvalidState(InvalidStates.INVALID_LIEN_ID);
2023-01-astaria/src/PublicVault.sol::241 => require(s.allowList[receiver]);
2023-01-astaria/src/Vault.sol::77 => revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
2023-01-astaria/src/VaultImplementation.sol::96 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::243 => revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::25 => require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::27 => require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::23 => revert MaxAmountError();
2023-01-astaria/src/astaria-gpl/src/ERC4626RouterBase.sol::36 => revert MinSharesError();
2023-01-astaria/src/astaria-gpl/src/RC721.sol::115 => require(to != address(0), "INVALID_RECIPIENT");
```


### [G06] Use custom errors rather than `revert()`/`require()` strings to save deployment gas



#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::398 => revert("unsupported/impl");
2023-01-astaria/src/ClearingHouse.sol::134 => require(payment >= currentOfferPrice, "not enough funds received");
2023-01-astaria/src/PublicVault.sol::170 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
2023-01-astaria/src/WithdrawProxy.sol::138 => require(msg.sender == VAULT(), "only vault can mint");
2023-01-astaria/src/WithdrawProxy.sol::231 => require(msg.sender == VAULT(), "only vault can call");
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::115 => require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::25 => require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::27 => require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::43 => require(assets > minDepositAmount(), "VALUE_TOO_SMALL");
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::94 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::60 => require(owner != address(0), "ZERO_ADDRESS");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::113 => require(from == s._ownerOf[id], "WRONG_FROM");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::115 => require(to != address(0), "INVALID_RECIPIENT");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::200 => require(to != address(0), "INVALID_RECIPIENT");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::202 => require(s._ownerOf[id] == address(0), "ALREADY_MINTED");
2023-01-astaria/src/astaria-gpl/src/RC721.sol::219 => require(owner != address(0), "NOT_MINTED");
```



### [G07] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
2023-01-astaria/src/PublicVault.sol::534 => function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::562 => function afterPayment(uint256 computedSlope) public onlyLienToken {
2023-01-astaria/src/WithdrawProxy.sol::235 => function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```






### [G08] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/RC721.sol::69 => function __initERC721(string memory _name, string memory _symbol) internal {
```






### [G09] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.
#### Findings:
```
2023-01-astaria/src/AstariaRouter.sol::519 => .safeTransfer(msg.sender, totalBorrowed);
2023-01-astaria/src/LienToken.sol::374 => super.transferFrom(from, to, id);
```




### [G10] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::22 => mapping(address => uint256) balanceOf;
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::24 => mapping(address => uint256) nonces;
2023-01-astaria/src/astaria-gpl/src/RC721.sol::20 => mapping(uint256 => address) _ownerOf;
2023-01-astaria/src/astaria-gpl/src/RC721.sol::22 => mapping(uint256 => address) getApproved;
```



### [G11] Using `bools` for storage incurs overhead


#### Findings:
```
2023-01-astaria/src/astaria-gpl/src/RC721.sol::23 => mapping(address => mapping(address => bool)) isApprovedForAll;
```

### [G12] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code. Savings are due to the compiler not having to create non-payable getter functions for deployment call data, and not adding another entry to the method ID table
#### Findings:
```
2023-01-astaria/src/VaultImplementation.sol::44 => bytes32 public constant STRATEGY_TYPEHASH =
```


### [G13] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
2023-01-astaria/src/BeaconProxy.sol::87 => function _beforeFallback() internal virtual {}
2023-01-astaria/src/ClearingHouse.sol::104 => function setApprovalForAll(address operator, bool approved) external {}
2023-01-astaria/src/ClearingHouse.sol::186 => ) public {}
2023-01-astaria/src/VaultImplementation.sol::272 => ) internal virtual {}
2023-01-astaria/src/VaultImplementation.sol::277 => {}
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::165 => function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
2023-01-astaria/src/astaria-gpl/src/ERC4626-Cloned.sol::167 => function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```




### [G14] `abi.encode()` is less efficient than abi.encodePacked()



#### Findings:
```
2023-01-astaria/src/CollateralToken.sol::124 => s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
2023-01-astaria/src/CollateralToken.sol::520 => abi.encode(listingOrder.parameters)
2023-01-astaria/src/LienToken.sol::229 => abi.encode(newStack)
2023-01-astaria/src/LienToken.sol::271 => if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
2023-01-astaria/src/LienToken.sol::406 => abi.encode(newStack)
2023-01-astaria/src/LienToken.sol::448 => newLienId = uint256(keccak256(abi.encode(params.lien)));
2023-01-astaria/src/LienToken.sol::563 => lienId = uint256(keccak256(abi.encode(lien)));
2023-01-astaria/src/LienToken.sol::698 => s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));
2023-01-astaria/src/VaultImplementation.sol::155 => abi.encode(
2023-01-astaria/src/VaultImplementation.sol::184 => abi.encode(STRATEGY_TYPEHASH, s.strategistNonce, strategy.deadline, root)
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::127 => abi.encode(
2023-01-astaria/src/astaria-gpl/src/ERC20-Cloned.sol::161 => abi.encode(
```

