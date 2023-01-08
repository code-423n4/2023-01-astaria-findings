# QA REPORT

## [L-1] Use of `block.timestamp`

### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### ✅ Recommendation
Use oracle instead of `block.timestamp`

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/AstariaRouter.sol#L439|[if (block.timestamp > commitment.lienRequest.strategy.deadline) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L439 )|
|2023-01-astaria/src/AstariaRouter.sol#L617|[return (stack.point.end <= block.timestamp ||](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L617 )|
|2023-01-astaria/src/AstariaRouter.sol#L698|[newLien.details.duration + block.timestamp >=](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L698 )|
|2023-01-astaria/src/AstariaRouter.sol#L700|[(block.timestamp + newLien.details.duration - stack[position].point.end >=](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L700 )|
|2023-01-astaria/src/AstariaRouter.sol#L738|[block.timestamp,](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L738 )|
|2023-01-astaria/src/CollateralToken.sol#L132|[if (block.timestamp < params.endTime) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L132 )|
|2023-01-astaria/src/CollateralToken.sol#L468|[startTime: uint256(block.timestamp),](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L468 )|
|2023-01-astaria/src/CollateralToken.sol#L469|[endTime: uint256(block.timestamp + maxDuration),](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L469 )|
|2023-01-astaria/src/LienToken.sol#L112|[if (block.timestamp >= params.encumber.stack[params.position].point.end) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L112 )|
|2023-01-astaria/src/LienToken.sol#L156|[if (block.timestamp >= params.encumber.stack[j].point.end) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L156 )|
|2023-01-astaria/src/LienToken.sol#L247|[return _getInterest(stack, block.timestamp);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L247 )|
|2023-01-astaria/src/LienToken.sol#L309|[uint88 owed = _getOwed(stack[i], block.timestamp);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L309 )|
|2023-01-astaria/src/LienToken.sol#L335|[auctionData.startTime = block.timestamp.safeCastTo48();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L335 )|
|2023-01-astaria/src/LienToken.sol#L336|[auctionData.endTime = (block.timestamp + auctionWindow).safeCastTo48();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L336 )|
|2023-01-astaria/src/LienToken.sol#L452|[last: block.timestamp.safeCastTo40(),](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L452 )|
|2023-01-astaria/src/LienToken.sol#L453|[end: (block.timestamp + params.lien.details.duration).safeCastTo40()](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L453 )|
|2023-01-astaria/src/LienToken.sol#L475|[if (block.timestamp >= newStack[j].point.end) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L475 )|
|2023-01-astaria/src/LienToken.sol#L590|[owed = _getOwed(stack, block.timestamp);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L590 )|
|2023-01-astaria/src/LienToken.sol#L744|[return _getOwed(stack, block.timestamp);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L744 )|
|2023-01-astaria/src/LienToken.sol#L780|[uint256 delta_t = stack.point.end - block.timestamp;](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L780 )|
|2023-01-astaria/src/LienToken.sol#L805|[if (block.timestamp >= end) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L805 )|
|2023-01-astaria/src/LienToken.sol#L808|[uint256 owed = _getOwed(stack, block.timestamp);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L808 )|
|2023-01-astaria/src/LienToken.sol#L827|[stack.point.last = block.timestamp.safeCastTo40();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L827 )|
|2023-01-astaria/src/PublicVault.sol#L468|[s.last = block.timestamp.safeCastTo40();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L468 )|
|2023-01-astaria/src/PublicVault.sol#L491|[uint256 delta_t = block.timestamp - s.last;](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L491 )|
|2023-01-astaria/src/PublicVault.sol#L625|[s.last = block.timestamp.safeCastTo40();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L625 )|
|2023-01-astaria/src/PublicVault.sol#L706|[if (block.timestamp >= epochEnd) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L706 )|
|2023-01-astaria/src/PublicVault.sol#L710|[return epochEnd - block.timestamp;](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L710 )|
|2023-01-astaria/src/WithdrawProxy.sol#L250|[if (block.timestamp < s.finalAuctionEnd) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L250 )|
|2023-01-astaria/src/WithdrawProxy.sol#L314|[uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L314 )|
---

## [L-2] `_safeMint()` should be used rather than `_mint()` wherever possible.

### Description
`_safeMint()` is used along with [IERC721Receiver](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721Receiver) that checks if you are sending the minted token to a Contract that is capable to manage NFTs or not. This is to prevent tokens to be lost.

### ✅ Recommendation
Change from` _mint` to `_safeMint`.

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/CollateralToken.sol#L588|[_mint(from_, collateralId);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L588 )|
|2023-01-astaria/src/LienToken.sol#L455|[_mint(params.receiver, newLienId);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L455 )|
|2023-01-astaria/src/PublicVault.sol#L512|[_mint(msg.sender, unclaimed);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L512 )|
|2023-01-astaria/src/WithdrawProxy.sol#L139|[_mint(receiver, shares);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L139 )|
---


## [N-1] Lines are too long.

### Description
Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

### ✅ Recommendation
Reduce number of characters per line to improve readability.

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/AstariaRouter.sol#L75|[* @dev Setup transfer authority and set up addresses for deployed CollateralToken, LienToken, TransferProxy contracts, as well as PublicVault and SoloVault implementations to clone.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L75 )|
|2023-01-astaria/src/LienToken.sol#L42|[* @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L42 )|
|2023-01-astaria/src/VaultImplementation.sol#L282|[* Starts by depositing collateral and take optimized-out a lien against it. Next, verifies the merkle proof for a loan commitment. Vault owners are then rewarded fees for successful loan origination.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L282 )|
|2023-01-astaria/src/VaultImplementation.sol#L363|[* @notice Retrieves the recipient of loan repayments. For PublicVaults (VAULT_TYPE 2), this is always the vault address. For PrivateVaults, retrieves the owner() of the vault.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L363 )|
|2023-01-astaria/src/WithdrawProxy.sol#L54|[uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54 )|
|2023-01-astaria/src/WithdrawProxy.sol#L56|[uint256 withdrawReserveReceived; // amount received from PublicVault. The WETH balance of this contract - withdrawReserveReceived = amount received from liquidations.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L56 )|
|2023-01-astaria/src/interfaces/ICollateralToken.sol#L100|[* @notice Executes a FlashAction using locked collateral. A valid FlashAction performs a specified action with the collateral within a single transaction and must end with the collateral being returned to the Vault it was locked in.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L100 )|
|2023-01-astaria/src/interfaces/IWithdrawProxy.sol#L27|[* @notice Called at epoch boundary, computes the ratio between the funds of withdrawing liquidity providers and the balance of the underlying PublicVault so that claim() proportionally pays optimized-out to all parties.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L27 )|
|2023-01-astaria/src/interfaces/IWithdrawProxy.sol#L35|[* @param finalAuctionDelta The timestamp by which the auction being added is guaranteed to end. As new auctions are added to the WithdrawProxy, this value will strictly increase as all auctions have the same maximum duration.](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L35 )|
---
