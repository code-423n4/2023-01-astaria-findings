

## [L-01] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2023-01-astaria/src/AstariaRouter.sol::439 => if (block.timestamp > commitment.lienRequest.strategy.deadline) {
2023-01-astaria/src/AstariaRouter.sol::617 => return (stack.point.end <= block.timestamp ||
2023-01-astaria/src/AstariaRouter.sol::698 => newLien.details.duration + block.timestamp >=
2023-01-astaria/src/AstariaRouter.sol::700 => (block.timestamp + newLien.details.duration - stack[position].point.end >=
2023-01-astaria/src/AstariaRouter.sol::738 => block.timestamp,
2023-01-astaria/src/CollateralToken.sol::132 => if (block.timestamp < params.endTime) {
2023-01-astaria/src/CollateralToken.sol::468 => startTime: uint256(block.timestamp),
2023-01-astaria/src/CollateralToken.sol::469 => endTime: uint256(block.timestamp + maxDuration),
2023-01-astaria/src/LienToken.sol::112 => if (block.timestamp >= params.encumber.stack[params.position].point.end) {
2023-01-astaria/src/LienToken.sol::156 => if (block.timestamp >= params.encumber.stack[j].point.end) {
2023-01-astaria/src/LienToken.sol::247 => return _getInterest(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::309 => uint88 owed = _getOwed(stack[i], block.timestamp);
2023-01-astaria/src/LienToken.sol::335 => auctionData.startTime = block.timestamp.safeCastTo48();
2023-01-astaria/src/LienToken.sol::336 => auctionData.endTime = (block.timestamp + auctionWindow).safeCastTo48();
2023-01-astaria/src/LienToken.sol::452 => last: block.timestamp.safeCastTo40(),
2023-01-astaria/src/LienToken.sol::453 => end: (block.timestamp + params.lien.details.duration).safeCastTo40()
2023-01-astaria/src/LienToken.sol::475 => if (block.timestamp >= newStack[j].point.end) {
2023-01-astaria/src/LienToken.sol::590 => owed = _getOwed(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::744 => return _getOwed(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::780 => uint256 delta_t = stack.point.end - block.timestamp;
2023-01-astaria/src/LienToken.sol::805 => if (block.timestamp >= end) {
2023-01-astaria/src/LienToken.sol::808 => uint256 owed = _getOwed(stack, block.timestamp);
2023-01-astaria/src/LienToken.sol::825 => //bring the point up to block.timestamp, compute the owed
2023-01-astaria/src/LienToken.sol::827 => stack.point.last = block.timestamp.safeCastTo40();
2023-01-astaria/src/PublicVault.sol::468 => s.last = block.timestamp.safeCastTo40();
2023-01-astaria/src/PublicVault.sol::491 => uint256 delta_t = block.timestamp - s.last;
2023-01-astaria/src/PublicVault.sol::625 => s.last = block.timestamp.safeCastTo40();
2023-01-astaria/src/PublicVault.sol::706 => if (block.timestamp >= epochEnd) {
2023-01-astaria/src/PublicVault.sol::710 => return epochEnd - block.timestamp;
2023-01-astaria/src/WithdrawProxy.sol::250 => if (block.timestamp < s.finalAuctionEnd) {
2023-01-astaria/src/WithdrawProxy.sol::314 => uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();
```

## [L-02] safeApprove() is deprecated

safeApprove() is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

```
2023-01-astaria/src/ClearingHouse.sol::148 => ERC20(paymentToken).safeApprove(
2023-01-astaria/src/VaultImplementation.sol::334 => ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
```

## [L-03] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
2023-01-astaria/src/PublicVault.sol::103 => if (ERC20(asset()).decimals() == uint8(18)) {
2023-01-astaria/src/PublicVault.sol::106 => return 10**(ERC20(asset()).decimals() - 1);
2023-01-astaria/src/WithdrawProxy.sol::273 => 10**ERC20(asset()).decimals()
```

## [L-04] pragma experimental ABIEncoderV2 is deprecated

Use pragma abicoder v2 instead

```
2023-01-astaria/src/CollateralToken.sol::16 => pragma experimental ABIEncoderV2;
2023-01-astaria/src/LienToken.sol::16 => pragma experimental ABIEncoderV2;
```

## [L-05] approve should be replaced with safeApprove or safeIncreaseAllowance() / safeDecreaseAllowance()

approve is subject to a known front-running attack. Consider using safeApprove() or safeIncreaseAllowance() or safeDecreaseAllowance() instead

```
2023-01-astaria/src/ClearingHouse.sol::203 => ERC721(order.parameters.offer[0].token).approve(
```

## [L-06] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-01-astaria/src/LienToken.sol::374 => super.transferFrom(from, to, id);
2023-01-astaria/src/actions/UNIV3/ClaimFees.sol::52 => ERC721(asset.token).transferFrom(
```

## [L-07] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
2023-01-astaria/src/AstariaRouter.sol::339 => function setNewGuardian(address _guardian) external {
2023-01-astaria/src/ClearingHouse.sol::66 => function setAuctionData(ILienToken.AuctionData calldata auctionData)
2023-01-astaria/src/ClearingHouse.sol::104 => function setApprovalForAll(address operator, bool approved) external {}
2023-01-astaria/src/ClearingHouse.sol::220 => function settleLiquidatorNFTClaim() external {
2023-01-astaria/src/CollateralToken.sol::524 => function settleAuction(uint256 collateralId) public {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

## [L-08] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2023-01-astaria/src/CollateralToken.sol::588 => _mint(from_, collateralId);
2023-01-astaria/src/LienToken.sol::455 => _mint(params.receiver, newLienId);
2023-01-astaria/src/PublicVault.sol::512 => _mint(msg.sender, unclaimed);
2023-01-astaria/src/WithdrawProxy.sol::139 => _mint(receiver, shares);
```

## [N-01] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2023-01-astaria/src/AstariaRouter.sol::67 => 0x571e08d100000000000000000000000000000000000000000000000000000000;
2023-01-astaria/src/PublicVault.sol::604 => uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

## [N-02] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2023-01-astaria/src/PublicVault.sol::459 => event SlopeUpdated(uint48 newSlope);
```

## [N-03] Missing NatSpec

Code should include NatSpec

```
2023-01-astaria/src/AstariaVaultBase.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/ClearingHouse.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/TransferProxy.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/WithdrawVaultBase.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/actions/UNIV3/ClaimFees.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/security/V3SecurityHook.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/strategies/CollectionValidator.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/strategies/UNI_V3Validator.sol::1 => // SPDX-License-Identifier: BUSL-1.1
2023-01-astaria/src/strategies/UniqueValidator.sol::1 => // SPDX-License-Identifier: BUSL-1.1
```

## [N-04] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
2023-01-astaria/src/AstariaRouter.sol::339 => function setNewGuardian(address _guardian) external {
2023-01-astaria/src/ClearingHouse.sol::66 => function setAuctionData(ILienToken.AuctionData calldata auctionData)
2023-01-astaria/src/ClearingHouse.sol::104 => function setApprovalForAll(address operator, bool approved) external {}
2023-01-astaria/src/ClearingHouse.sol::220 => function settleLiquidatorNFTClaim() external {
2023-01-astaria/src/CollateralToken.sol::524 => function settleAuction(uint256 collateralId) public {
2023-01-astaria/src/WithdrawProxy.sol::302 => function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
```

## [N-05] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2023-01-astaria/src/VaultImplementation.sol::51 => bytes32 constant VERSION = keccak256("0");
```

## [N-06] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2023-01-astaria/src/AstariaRouter.sol::75 => * @dev Setup transfer authority and set up addresses for deployed CollateralToken, LienToken, TransferProxy contracts, as well as PublicVault and SoloVault implementations to clone.
2023-01-astaria/src/LienToken.sol::42 => * @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.
2023-01-astaria/src/VaultImplementation.sol::282 => * Starts by depositing collateral and take optimized-out a lien against it. Next, verifies the merkle proof for a loan commitment. Vault owners are then rewarded fees for successful loan origination.
2023-01-astaria/src/VaultImplementation.sol::363 => * @notice Retrieves the recipient of loan repayments. For PublicVaults (VAULT_TYPE 2), this is always the vault address. For PrivateVaults, retrieves the owner() of the vault.
2023-01-astaria/src/WithdrawProxy.sol::54 => uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.
2023-01-astaria/src/WithdrawProxy.sol::56 => uint256 withdrawReserveReceived; // amount received from PublicVault. The WETH balance of this contract - withdrawReserveReceived = amount received from liquidations.
```

