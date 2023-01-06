## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2023-01-astaria/src/LienToken.sol::152 => uint256 potentialDebt = 0;
2023-01-astaria/src/LienToken.sol::636 => uint256 remaining = 0;
2023-01-astaria/src/WithdrawProxy.sol::254 => uint256 transferAmount = 0;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

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

## [G-03] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2023-01-astaria/src/AstariaRouter.sol::115 => s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();
```

## [G-04] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2023-01-astaria/src/LienToken.sol::562 => function validateLien(Lien memory lien) public view returns (uint256 lienId) {
2023-01-astaria/src/LienToken.sol::702 => function calculateSlope(Stack memory stack) public pure returns (uint256) {
2023-01-astaria/src/LienToken.sol::742 => function getOwed(Stack memory stack) external view returns (uint88) {
```

## [G-05] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
2023-01-astaria/src/actions/UNIV3/ClaimFees.sol::22 => address public immutable positionManager;
```

## [G-06] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
2023-01-astaria/src/PublicVault.sol::534 => function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::562 => function afterPayment(uint256 computedSlope) public onlyLienToken {
2023-01-astaria/src/WithdrawProxy.sol::235 => function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2023-01-astaria/src/AstariaRouter.sol::329 => (uint8 TYPE, address addr) = abi.decode(data, (uint8, address));
2023-01-astaria/src/AstariaRouter.sol::442 => uint8 nlrType = uint8(_sliceUint(commitment.lienRequest.nlrDetails, 0));
2023-01-astaria/src/AstariaRouter.sol::722 => uint8 vaultType;
2023-01-astaria/src/LienToken.sol::309 => uint88 owed = _getOwed(stack[i], block.timestamp);
2023-01-astaria/src/LienToken.sol::803 => uint64 end = stack.point.end;
2023-01-astaria/src/PublicVault.sol::453 => uint64 epoch = getLienEpoch(lienEnd);
2023-01-astaria/src/PublicVault.sol::605 => uint88 feeInShares = convertToShares(fee).safeCastTo88();
2023-01-astaria/src/PublicVault.sol::654 => uint64 lienEpoch = getLienEpoch(params.lienEnd);
2023-01-astaria/src/PublicVault.sol::671 => uint64 currentEpoch = s.currentEpoch;
2023-01-astaria/src/PublicVault.sol::686 => uint64 currentEpoch = s.currentEpoch;
2023-01-astaria/src/WithdrawProxy.sol::53 => uint88 withdrawRatio;
2023-01-astaria/src/WithdrawProxy.sol::54 => uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.
2023-01-astaria/src/strategies/CollectionValidator.sol::24 => uint8 version;
2023-01-astaria/src/strategies/CollectionValidator.sol::32 => uint8 public constant VERSION_TYPE = uint8(2);
2023-01-astaria/src/strategies/UNI_V3Validator.sol::30 => uint8 version;
2023-01-astaria/src/strategies/UNI_V3Validator.sol::38 => uint128 minLiquidity;
2023-01-astaria/src/strategies/UNI_V3Validator.sol::57 => uint8 public constant VERSION_TYPE = uint8(3);
2023-01-astaria/src/strategies/UniqueValidator.sol::24 => uint8 version;
2023-01-astaria/src/strategies/UniqueValidator.sol::33 => uint8 public constant VERSION_TYPE = uint8(1);
```

## [G-08] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2023-01-astaria/src/AstariaRouter.sol::268 => ++i;
2023-01-astaria/src/AstariaRouter.sol::388 => ++i;
2023-01-astaria/src/AstariaRouter.sol::514 => ++i;
2023-01-astaria/src/ClearingHouse.sol::99 => ++i;
2023-01-astaria/src/CollateralToken.sol::201 => ++i;
2023-01-astaria/src/LienToken.sol::224 => ++i;
2023-01-astaria/src/LienToken.sol::331 => ++i;
2023-01-astaria/src/LienToken.sol::526 => ++i;
2023-01-astaria/src/LienToken.sol::683 => ++i;
2023-01-astaria/src/LienToken.sol::722 => ++i;
2023-01-astaria/src/LienToken.sol::737 => ++i;
2023-01-astaria/src/LienToken.sol::866 => ++i;
2023-01-astaria/src/LienToken.sol::872 => ++i;
2023-01-astaria/src/PublicVault.sol::341 => s.currentEpoch++;
2023-01-astaria/src/PublicVault.sol::558 => s.epochData[epoch].liensOpenForEpoch++;
2023-01-astaria/src/VaultImplementation.sol::69 => s.strategistNonce++;
2023-01-astaria/src/VaultImplementation.sol::204 => ++i;
```

## [G-09] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
2023-01-astaria/src/AstariaRouter.sol::512 => totalBorrowed += payout;
2023-01-astaria/src/LienToken.sol::160 => potentialDebt += _getOwed(
2023-01-astaria/src/LienToken.sol::210 => maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);
2023-01-astaria/src/LienToken.sol::480 => potentialDebt += _getOwed(newStack[j], newStack[j].point.end);
2023-01-astaria/src/LienToken.sol::524 => totalSpent += spent;
2023-01-astaria/src/LienToken.sol::525 => payment -= spent;
2023-01-astaria/src/LienToken.sol::679 => totalCapitalAvailable -= spent;
2023-01-astaria/src/LienToken.sol::720 => maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);
2023-01-astaria/src/LienToken.sol::735 => maxPotentialDebt += _getOwed(stack[i], end);
2023-01-astaria/src/LienToken.sol::830 => stack.point.amount -= amount.safeCastTo88();
2023-01-astaria/src/PublicVault.sol::174 => es.balanceOf[owner] -= shares;
2023-01-astaria/src/PublicVault.sol::179 => es.balanceOf[address(this)] += shares;
2023-01-astaria/src/PublicVault.sol::380 => s.withdrawReserve -= withdrawBalance.safeCastTo88();
2023-01-astaria/src/PublicVault.sol::405 => s.withdrawReserve -= drainBalance.safeCastTo88();
2023-01-astaria/src/PublicVault.sol::565 => s.slope += computedSlope.safeCastTo48();
2023-01-astaria/src/PublicVault.sol::583 => s.yIntercept += assets.safeCastTo88();
2023-01-astaria/src/PublicVault.sol::606 => s.strategistUnclaimedShares += feeInShares;
2023-01-astaria/src/PublicVault.sol::624 => s.yIntercept += params.increaseYIntercept.safeCastTo88();
2023-01-astaria/src/VaultImplementation.sol::404 => amount -= fee;
2023-01-astaria/src/WithdrawProxy.sol::237 => s.withdrawReserveReceived += amount;
2023-01-astaria/src/WithdrawProxy.sol::277 => balance -= transferAmount;
2023-01-astaria/src/WithdrawProxy.sol::313 => s.expected += newLienExpectedValue.safeCastTo88();
```

## [G-10] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

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
2023-01-astaria/src/security/V3SecurityHook.sol::44 => return keccak256(abi.encode(nonce, operator, liquidity));
2023-01-astaria/src/strategies/CollectionValidator.sol::47 => return abi.encode(details);
2023-01-astaria/src/strategies/UNI_V3Validator.sol::69 => return abi.encode(details);
2023-01-astaria/src/strategies/UniqueValidator.sol::48 => return abi.encode(details);
```

## [G-11] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
2023-01-astaria/src/AuthInitializable.sol::43 => require(s.owner == address(0), "Already initialized");
2023-01-astaria/src/AuthInitializable.sol::52 => require(isAuthorized(msg.sender, msg.sig), "UNAUTHORIZED");
2023-01-astaria/src/ClearingHouse.sol::134 => require(payment >= currentOfferPrice, "not enough funds received");
2023-01-astaria/src/PublicVault.sol::170 => require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");
2023-01-astaria/src/PublicVault.sol::508 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::78 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::96 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::105 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::114 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::147 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/VaultImplementation.sol::211 => require(msg.sender == owner()); //owner is "strategist"
2023-01-astaria/src/WithdrawProxy.sol::138 => require(msg.sender == VAULT(), "only vault can mint");
2023-01-astaria/src/WithdrawProxy.sol::231 => require(msg.sender == VAULT(), "only vault can call");
2023-01-astaria/src/strategies/CollectionValidator.sol::72 => require(cd.token == collateralTokenContract, "invalid token contract");
2023-01-astaria/src/strategies/UniqueValidator.sol::74 => require(cd.token == collateralTokenContract, "invalid token contract");
2023-01-astaria/src/strategies/UniqueValidator.sol::76 => require(cd.tokenId == collateralTokenId, "invalid token id");
```

## [G-12] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2023-01-astaria/src/Vault.sol::65 => require(s.allowList[msg.sender] && receiver == owner());
```

## [G-13] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2023-01-astaria/src/AstariaRouter.sol::224 => function feeTo() public view returns (address) {
2023-01-astaria/src/AstariaRouter.sol::229 => function BEACON_PROXY_IMPLEMENTATION() public view returns (address) {
2023-01-astaria/src/AstariaRouter.sol::234 => function LIEN_TOKEN() public view returns (ILienToken) {
2023-01-astaria/src/AstariaRouter.sol::239 => function TRANSFER_PROXY() public view returns (ITransferProxy) {
2023-01-astaria/src/AstariaRouter.sol::244 => function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
2023-01-astaria/src/AstariaRouter.sol::402 => function getAuctionWindow(bool includeBuffer) public view returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::32 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/AstariaVaultBase.sol::36 => function owner() public pure returns (address) {
2023-01-astaria/src/AstariaVaultBase.sol::50 => function START() public pure returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::54 => function EPOCH_LENGTH() public pure returns (uint256) {
2023-01-astaria/src/AstariaVaultBase.sol::58 => function VAULT_FEE() public pure returns (uint256) {
2023-01-astaria/src/AuthInitializable.sol::57 => function owner() public view returns (address) {
2023-01-astaria/src/AuthInitializable.sol::61 => function authority() public view returns (Authority) {
2023-01-astaria/src/AuthInitializable.sol::81 => function setAuthority(Authority newAuthority) public virtual {
2023-01-astaria/src/AuthInitializable.sol::95 => function transferOwnership(address newOwner) public virtual requiresAuth {
2023-01-astaria/src/ClearingHouse.sol::43 => function ROUTER() public pure returns (IAstariaRouter) {
2023-01-astaria/src/ClearingHouse.sol::51 => function IMPL_TYPE() public pure returns (uint8) {
2023-01-astaria/src/CollateralToken.sol::105 => function SEAPORT() public view returns (ConsiderationInterface) {
2023-01-astaria/src/CollateralToken.sol::370 => function getConduitKey() public view returns (bytes32) {
2023-01-astaria/src/CollateralToken.sol::412 => function securityHooks(address target) public view returns (address) {
2023-01-astaria/src/LienToken.sol::377 => function ASTARIA_ROUTER() public view returns (IAstariaRouter) {
2023-01-astaria/src/LienToken.sol::381 => function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
2023-01-astaria/src/PublicVault.sol::192 => function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {
2023-01-astaria/src/PublicVault.sol::196 => function getCurrentEpoch() public view returns (uint64) {
2023-01-astaria/src/PublicVault.sol::200 => function getSlope() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::204 => function getWithdrawReserve() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::208 => function getLiquidationWithdrawRatio() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::212 => function getYIntercept() public view returns (uint256) {
2023-01-astaria/src/PublicVault.sol::552 => function getEpochEnd(uint256 epoch) public pure returns (uint64) {
2023-01-astaria/src/PublicVault.sol::562 => function afterPayment(uint256 computedSlope) public onlyLienToken {
2023-01-astaria/src/PublicVault.sol::669 => function increaseYIntercept(uint256 amount) public {
2023-01-astaria/src/PublicVault.sol::684 => function decreaseYIntercept(uint256 amount) public {
2023-01-astaria/src/PublicVault.sol::722 => function timeToSecondEpochEnd() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::215 => function getFinalAuctionEnd() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::220 => function getWithdrawRatio() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::225 => function getExpected() public view returns (uint256) {
2023-01-astaria/src/WithdrawProxy.sol::240 => function claim() public {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
2023-01-astaria/src/WithdrawVaultBase.sol::22 => function name() public view virtual returns (string memory);
2023-01-astaria/src/WithdrawVaultBase.sol::24 => function symbol() public view virtual returns (string memory);
2023-01-astaria/src/WithdrawVaultBase.sol::30 => function IMPL_TYPE() public pure override(IRouterBase) returns (uint8) {
2023-01-astaria/src/WithdrawVaultBase.sol::34 => function asset() public pure virtual override(IERC4626) returns (address) {
2023-01-astaria/src/WithdrawVaultBase.sol::38 => function VAULT() public pure returns (address) {
2023-01-astaria/src/WithdrawVaultBase.sol::42 => function CLAIMABLE_EPOCH() public pure returns (uint64) {
```

## [G-14] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
2023-01-astaria/src/AstariaRouter.sol::134 => returns (uint256 amountIn)
2023-01-astaria/src/AstariaRouter.sol::150 => returns (uint256 sharesOut)
2023-01-astaria/src/AstariaRouter.sol::166 => returns (uint256 sharesOut)
2023-01-astaria/src/AstariaRouter.sol::182 => returns (uint256 amountOut)
2023-01-astaria/src/AstariaRouter.sol::192 => ) public virtual validVault(address(vault)) returns (uint256 assets) {
2023-01-astaria/src/AstariaRouter.sol::721 => ) internal returns (address vaultAddr) {
2023-01-astaria/src/CollateralToken.sol::388 => returns (address, uint256)
2023-01-astaria/src/LienToken.sol::110 => returns (Stack[] memory, Stack memory newStack)
2023-01-astaria/src/LienToken.sol::427 => ) internal returns (uint256 newLienId, ILienToken.Stack memory newSlot) {
2023-01-astaria/src/LienToken.sol::580 => returns (uint256 owed, uint256 buyout)
2023-01-astaria/src/LienToken.sol::603 => returns (Stack[] memory newStack)
2023-01-astaria/src/LienToken.sol::710 => returns (uint256 maxPotentialDebt)
2023-01-astaria/src/LienToken.sol::796 => ) internal returns (Stack[] memory, uint256) {
2023-01-astaria/src/PublicVault.sol::717 => returns (uint256 timeToSecondEpochEnd)
2023-01-astaria/src/VaultImplementation.sol::357 => returns (uint256 timeToSecondEpochEnd)
2023-01-astaria/src/WithdrawProxy.sol::136 => returns (uint256 assets)
2023-01-astaria/src/WithdrawProxy.sol::172 => returns (uint256 shares)
2023-01-astaria/src/WithdrawProxy.sol::193 => returns (uint256 assets)
```

## [G-15] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2023-01-astaria/src/AuthInitializable.sol::41 => function __initAuth(address _owner, address _authority) internal {
2023-01-astaria/src/BeaconProxy.sol::20 => function _getBeacon() internal pure returns (IBeacon) {
2023-01-astaria/src/BeaconProxy.sol::27 => * This function does not return to its internal call site, it will return directly to the external caller.
2023-01-astaria/src/BeaconProxy.sol::29 => function _delegate(address implementation) internal virtual {
2023-01-astaria/src/BeaconProxy.sol::57 => * This function does not return to its internal call site, it will return directly to the external caller.
2023-01-astaria/src/PublicVault.sol::556 => function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
2023-01-astaria/src/VaultImplementation.sol::397 => function _handleProtocolFee(uint256 amount) internal returns (uint256) {
```

## [G-16] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
2023-01-astaria/src/AuthInitializable.sol::41 => function __initAuth(address _owner, address _authority) internal {
2023-01-astaria/src/BeaconProxy.sol::27 => * This function does not return to its internal call site, it will return directly to the external caller.
2023-01-astaria/src/BeaconProxy.sol::57 => * This function does not return to its internal call site, it will return directly to the external caller.
```