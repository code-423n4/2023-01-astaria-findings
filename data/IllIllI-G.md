## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Structs can be packed into fewer storage slots | 2 |  - |
| [G&#x2011;02] | Using `storage` instead of `memory` for structs/arrays saves gas | 2 |  8400 |
| [G&#x2011;03] | The result of function calls should be cached rather than re-calling the function | 3 |  - |
| [G&#x2011;04] | `internal` functions only called once can be inlined to save gas | 21 |  420 |
| [G&#x2011;05] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |  85 |
| [G&#x2011;06] | `keccak256()` should only need to be called on a specific string literal once | 3 |  126 |
| [G&#x2011;07] | Optimize names to save gas | 18 |  396 |
| [G&#x2011;08] | Use a more recent version of solidity | 3 |  - |
| [G&#x2011;09] | Remove unused local variable | 1 |  - |
| [G&#x2011;10] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 3 |  15 |
| [G&#x2011;11] | Splitting `require()` statements that use `&&` saves gas | 4 |  12 |
| [G&#x2011;12] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 2 |  - |
| [G&#x2011;13] | Inverting the condition of an `if`-`else`-statement wastes gas | 1 |  - |
| [G&#x2011;14] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 1 |  - |
| [G&#x2011;15] | Use custom errors rather than `revert()`/`require()` strings to save gas | 8 |  - |
| [G&#x2011;16] | Functions guaranteed to revert when called by normal users can be marked `payable` | 21 |  441 |

Total: 94 instances over 16 issues with **9895 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 2 instances of this issue:*

```solidity
File: src/interfaces/IAstariaRouter.sol

/// @audit Variable ordering with 11 slots instead of the current 12:
///           mapping(32):strategyValidators, mapping(32):implementations, mapping(32):vaults, user-defined(20):WETH, uint88(11):maxInterestRate, address(20):COLLATERAL_TOKEN, uint32(4):auctionWindow, uint32(4):auctionWindowBuffer, uint32(4):liquidationFeeNumerator, address(20):LIEN_TOKEN, uint32(4):liquidationFeeDenominator, uint32(4):maxEpochLength, uint32(4):minEpochLength, address(20):TRANSFER_PROXY, uint32(4):protocolFeeNumerator, uint32(4):protocolFeeDenominator, uint32(4):minInterestBPS, address(20):feeTo, uint32(4):buyoutFeeNumerator, uint32(4):buyoutFeeDenominator, uint32(4):minDurationIncrease, address(20):BEACON_PROXY_IMPLEMENTATION, address(20):guardian, address(20):newGuardian
56      struct RouterStorage {
57        //slot 1
58        uint32 auctionWindow;
59        uint32 auctionWindowBuffer;
60        uint32 liquidationFeeNumerator;
61        uint32 liquidationFeeDenominator;
62        uint32 maxEpochLength;
63        uint32 minEpochLength;
64        uint32 protocolFeeNumerator;
65        uint32 protocolFeeDenominator;
66        //slot 2
67        ERC20 WETH; //20
68        ICollateralToken COLLATERAL_TOKEN; //20
69        ILienToken LIEN_TOKEN; //20
70        ITransferProxy TRANSFER_PROXY; //20
71        address feeTo; //20
72        address BEACON_PROXY_IMPLEMENTATION; //20
73        uint88 maxInterestRate; //6
74        uint32 minInterestBPS; // was uint64
75        //slot 3 +
76        address guardian; //20
77        address newGuardian; //20
78        uint32 buyoutFeeNumerator;
79        uint32 buyoutFeeDenominator;
80        uint32 minDurationIncrease;
81        mapping(uint8 => address) strategyValidators;
82        mapping(uint8 => address) implementations;
83        //A strategist can have many deployed vaults
84        mapping(address => bool) vaults;
85:     }

/// @audit Variable ordering with 2 slots instead of the current 3:
///           uint256(32):deadline, address(20):vault, uint8(1):version
101     struct StrategyDetailsParam {
102       uint8 version;
103       uint256 deadline;
104       address vault;
105:    }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L56-L85

### [G&#x2011;02]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 2 instances of this issue:*

```solidity
File: src/CollateralToken.sol

137:      Asset memory underlying = s.idToUnderlying[collateralId];

562:      Asset memory incomingAsset = s.idToUnderlying[collateralId];

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L137

### [G&#x2011;03]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There are 3 instances of this issue:*

```solidity
File: src/ClearingHouse.sol

/// @audit ASTARIA_ROUTER.COLLATERAL_TOKEN() on line 162
166:      ASTARIA_ROUTER.COLLATERAL_TOKEN().settleAuction(collateralId);

/// @audit ASTARIA_ROUTER.COLLATERAL_TOKEN() on line 199
204:        ASTARIA_ROUTER.COLLATERAL_TOKEN().getConduit(),

/// @audit ASTARIA_ROUTER.COLLATERAL_TOKEN() on line 199
207:      ASTARIA_ROUTER.COLLATERAL_TOKEN().SEAPORT().validate(listings);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L166

### [G&#x2011;04]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 21 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

407     function _sliceUint(bytes memory bs, uint256 start)
408       internal
409       pure
410:      returns (uint256 x)

761     function _executeCommitment(
762       RouterStorage storage s,
763       IAstariaRouter.Commitment memory c
764     )
765       internal
766       returns (
767         uint256,
768         ILienToken.Stack[] memory stack,
769:        uint256 payout

788     function _transferAndDepositAssetIfAble(
789       RouterStorage storage s,
790       address tokenContract,
791:      uint256 tokenId

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L407-L410

```solidity
File: src/BeaconProxy.sol

20:     function _getBeacon() internal pure returns (IBeacon) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/BeaconProxy.sol#L20

```solidity
File: src/ClearingHouse.sol

114     function _execute(
115       address tokenContract, // collateral token sending the fake nft
116       address to, // buyer
117       uint256 encodedMetaData, //retrieve token address from the encoded data
118:      uint256 // space to encode whatever is needed,

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L114-L118

```solidity
File: src/CollateralToken.sol

425     function _generateValidOrderParameters(
426       CollateralStorage storage s,
427       address settlementToken,
428       uint256 collateralId,
429       uint256[] memory prices,
430       uint256 maxDuration
431:    ) internal returns (OrderParameters memory orderParameters) {

502     function _listUnderlyingOnSeaport(
503       CollateralStorage storage s,
504       uint256 collateralId,
505:      Order memory listingOrder

541:    function _settleAuction(CollateralStorage storage s, uint256 collateralId)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L425-L431

```solidity
File: src/LienToken.sol

122     function _buyoutLien(
123       LienStorage storage s,
124       ILienToken.LienActionBuyout calldata params
125:    ) internal returns (Stack[] memory newStack, Stack memory newLien) {

233     function _replaceStackAtPositionWithNewLien(
234       LienStorage storage s,
235       ILienToken.Stack[] calldata stack,
236       uint256 position,
237       Stack memory newLien,
238       uint256 oldLienId
239:    ) internal returns (ILienToken.Stack[] memory newStack) {

292     function _stopLiens(
293       LienStorage storage s,
294       uint256 collateralId,
295       uint256 auctionWindow,
296       Stack[] calldata stack,
297:      address liquidator

459     function _appendStack(
460       LienStorage storage s,
461       Stack[] memory stack,
462       Stack memory newSlot
463:    ) internal returns (Stack[] memory newStack) {

512     function _payDebt(
513       LienStorage storage s,
514       address token,
515       uint256 payment,
516       address payer,
517       AuctionStack[] memory stack
518:    ) internal returns (uint256 totalSpent) {

623     function _paymentAH(
624       LienStorage storage s,
625       address token,
626       AuctionStack[] memory stack,
627       uint256 position,
628       uint256 payment,
629       address payer
630:    ) internal returns (uint256) {

663     function _makePayment(
664       LienStorage storage s,
665       Stack[] calldata stack,
666       uint256 totalCapitalAvailable
667:    ) internal returns (Stack[] memory newStack) {

715     function _getMaxPotentialDebtForCollateralUpToNPositions(
716       Stack[] memory stack,
717       uint256 n
718:    ) internal pure returns (uint256 maxPotentialDebt) {

775     function _getRemainingInterest(LienStorage storage s, Stack memory stack)
776       internal
777       view
778:      returns (uint256)

855     function _removeStackPosition(Stack[] memory stack, uint8 position)
856       internal
857:      returns (Stack[] memory newStack)

911     function _setPayee(
912       LienStorage storage s,
913       uint256 lienId,
914:      address newPayee

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L122-L125

```solidity
File: src/PublicVault.sol

556:    function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {

713     function _timeToSecondEndIfPublic()
714       internal
715       view
716       override
717:      returns (uint256 timeToSecondEpochEnd)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L556

### [G&#x2011;05]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*

```solidity
File: src/WithdrawProxy.sol

/// @audit if-condition on line 258
260:          (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L260

### [G&#x2011;06]  `keccak256()` should only need to be called on a specific string literal once
It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

*There are 3 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

162             keccak256(
163               "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
164:            ),

165:            keccak256("1"),

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L162-L164

```solidity
File: src/CollateralToken.sol

310:        ) != keccak256("FlashAction.onFlashAction")

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L310

### [G&#x2011;07]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 18 instances of this issue:*

```solidity
File: lib/gpl/src/interfaces/IERC4626Router.sol

/// @audit depositToVault(), depositMax(), redeemMax()
10:   interface IERC4626Router {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IERC4626Router.sol#L10

```solidity
File: src/AstariaRouter.sol

/// @audit initialize(), redeemFutureEpoch(), feeTo(), BEACON_PROXY_IMPLEMENTATION(), LIEN_TOKEN(), TRANSFER_PROXY(), COLLATERAL_TOKEN(), __emergencyPause(), __emergencyUnpause(), fileBatch(), file(), setNewGuardian(), __renounceGuardian(), __acceptGuardian(), fileGuardian(), getImpl(), getAuctionWindow(), validateCommitment(), commitToLiens(), newVault(), newPublicVault(), requestLienPosition(), canLiquidate(), liquidate(), getProtocolFee(), getLiquidatorFee(), getBuyoutFee(), isValidVault(), isValidRefinance()
50:   contract AstariaRouter is

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L50

```solidity
File: src/AstariaVaultBase.sol

/// @audit ROUTER(), IMPL_TYPE(), START(), EPOCH_LENGTH(), VAULT_FEE(), COLLATERAL_TOKEN()
23:   abstract contract AstariaVaultBase is Clone, IAstariaVaultBase {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaVaultBase.sol#L23

```solidity
File: src/ClearingHouse.sol

/// @audit ROUTER(), COLLATERAL_ID(), IMPL_TYPE(), setAuctionData(), validateOrder(), transferUnderlying(), settleLiquidatorNFTClaim()
32:   contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L32

```solidity
File: src/CollateralToken.sol

/// @audit initialize(), SEAPORT(), liquidatorNFTClaim(), isValidOrder(), isValidOrderIncludingExtraData(), fileBatch(), file(), flashAction(), releaseToAddress(), getConduitKey(), getConduit(), getUnderlying(), securityHooks(), getClearingHouse(), auctionVault(), settleAuction()
63:   contract CollateralToken is

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L63

```solidity
File: src/interfaces/IAstariaRouter.sol

/// @audit validateCommitment(), newPublicVault(), newVault(), feeTo(), commitToLiens(), requestLienPosition(), LIEN_TOKEN(), TRANSFER_PROXY(), BEACON_PROXY_IMPLEMENTATION(), COLLATERAL_TOKEN(), getAuctionWindow(), getProtocolFee(), getBuyoutFee(), getLiquidatorFee(), liquidate(), canLiquidate(), isValidVault(), fileBatch(), file(), setNewGuardian(), fileGuardian(), getImpl(), isValidRefinance()
28:   interface IAstariaRouter is IPausable, IBeacon {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaRouter.sol#L28

```solidity
File: src/interfaces/IAstariaVaultBase.sol

/// @audit COLLATERAL_TOKEN(), START(), EPOCH_LENGTH(), VAULT_FEE()
20:   interface IAstariaVaultBase is IRouterBase {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IAstariaVaultBase.sol#L20

```solidity
File: src/interfaces/ICollateralToken.sol

/// @audit fileBatch(), file(), flashAction(), securityHooks(), getConduit(), getConduitKey(), getClearingHouse(), auctionVault(), settleAuction(), SEAPORT(), getUnderlying(), releaseToAddress(), liquidatorNFTClaim()
31:   interface ICollateralToken is IERC721 {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ICollateralToken.sol#L31

```solidity
File: src/interfaces/ILienToken.sol

/// @audit validateLien(), ASTARIA_ROUTER(), COLLATERAL_TOKEN(), calculateSlope(), stopLiens(), getBuyout(), getOwed(), getOwed(), getInterest(), getCollateralState(), getAmountOwingAtLiquidation(), createLien(), buyoutLien(), payDebtViaClearingHouse(), makePayment(), makePayment(), getAuctionData(), getAuctionLiquidator(), getMaxPotentialDebtForCollateral(), getMaxPotentialDebtForCollateral(), getPayee(), file()
22:   interface ILienToken is IERC721 {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/ILienToken.sol#L22

```solidity
File: src/interfaces/IPublicVault.sol

/// @audit updateAfterLiquidationPayment(), redeemFutureEpoch(), beforePayment(), decreaseEpochLienCount(), getLienEpoch(), afterPayment(), claim(), timeToEpochEnd(), timeToSecondEpochEnd(), transferWithdrawReserve(), processEpoch(), increaseYIntercept(), decreaseYIntercept(), handleBuyoutLien(), updateVaultAfterLiquidation()
19:   interface IPublicVault is IVaultImplementation {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IPublicVault.sol#L19

```solidity
File: src/interfaces/IRouterBase.sol

/// @audit ROUTER(), IMPL_TYPE()
18:   interface IRouterBase {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IRouterBase.sol#L18

```solidity
File: src/interfaces/IVaultImplementation.sol

/// @audit getShutdown(), incrementNonce(), commitToLien(), buyoutLien(), recipient(), setDelegate(), init(), encodeStrategyData(), domainSeparator(), modifyDepositCap(), getStrategistNonce(), STRATEGY_TYPEHASH()
21:   interface IVaultImplementation is IAstariaVaultBase, IERC165 {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IVaultImplementation.sol#L21

```solidity
File: src/interfaces/IWithdrawProxy.sol

/// @audit VAULT(), CLAIMABLE_EPOCH(), setWithdrawRatio(), handleNewLiquidation(), drain(), claim(), increaseWithdrawReserveReceived(), getExpected(), getWithdrawRatio(), getFinalAuctionEnd()
21:   interface IWithdrawProxy is IRouterBase, IERC165, IERC4626 {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/interfaces/IWithdrawProxy.sol#L21

```solidity
File: src/LienToken.sol

/// @audit initialize(), file(), buyoutLien(), getInterest(), stopLiens(), ASTARIA_ROUTER(), COLLATERAL_TOKEN(), createLien(), payDebtViaClearingHouse(), getAuctionData(), getAuctionLiquidator(), getAmountOwingAtLiquidation(), validateLien(), getCollateralState(), getBuyout(), makePayment(), makePayment(), calculateSlope(), getMaxPotentialDebtForCollateral(), getMaxPotentialDebtForCollateral(), getOwed(), getOwed(), getPayee()
44:   contract LienToken is ERC721, ILienToken, AuthInitializable {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L44

```solidity
File: src/PublicVault.sol

/// @audit redeemFutureEpoch(), getWithdrawProxy(), getCurrentEpoch(), getSlope(), getWithdrawReserve(), getLiquidationWithdrawRatio(), getYIntercept(), processEpoch(), transferWithdrawReserve(), accrue(), claim(), beforePayment(), decreaseEpochLienCount(), getLienEpoch(), getEpochEnd(), afterPayment(), LIEN_TOKEN(), handleBuyoutLien(), updateAfterLiquidationPayment(), updateVaultAfterLiquidation(), increaseYIntercept(), decreaseYIntercept(), timeToEpochEnd(), timeToEpochEnd(), timeToSecondEpochEnd()
48:   contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L48

```solidity
File: src/VaultImplementation.sol

/// @audit getStrategistNonce(), incrementNonce(), modifyDepositCap(), modifyAllowList(), disableAllowList(), enableAllowList(), getShutdown(), domainSeparator(), encodeStrategyData(), init(), setDelegate(), commitToLien(), buyoutLien(), recipient()
34:   abstract contract VaultImplementation is

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L34

```solidity
File: src/WithdrawProxy.sol

/// @audit getFinalAuctionEnd(), getWithdrawRatio(), getExpected(), increaseWithdrawReserveReceived(), claim(), drain(), setWithdrawRatio(), handleNewLiquidation()
37:   contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L37

```solidity
File: src/WithdrawVaultBase.sol

/// @audit ROUTER(), VAULT(), CLAIMABLE_EPOCH()
21:   abstract contract WithdrawVaultBase is Clone, IWithdrawProxy {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawVaultBase.sol#L21

### [G&#x2011;08]  Use a more recent version of solidity
Use a solidity version of at least 0.8.0 to get overflow protection without `SafeMath`
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 3 instances of this issue:*

```solidity
File: lib/gpl/src/interfaces/IMulticall.sol

4:    pragma solidity >=0.7.5;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IMulticall.sol#L4

```solidity
File: lib/gpl/src/interfaces/IUniswapV3Factory.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IUniswapV3Factory.sol#L2

```solidity
File: lib/gpl/src/interfaces/IUniswapV3PoolState.sol

2:    pragma solidity >=0.5.0;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/interfaces/IUniswapV3PoolState.sol#L2

### [G&#x2011;09]  Remove unused local variable

*There is 1 instance of this issue:*

```solidity
File: src/ClearingHouse.sol

/// @audit stack
139:      ILienToken.AuctionStack[] storage stack = s.auctionStack.stack;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L139

### [G&#x2011;10]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 3 instances of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

136:        s._balanceOf[from]--;

223:        s._balanceOf[owner]--;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L136

```solidity
File: src/PublicVault.sol

539:      s.epochData[epoch].liensOpenForEpoch--;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L539

### [G&#x2011;11]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 4 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

143         require(
144           recoveredAddress != address(0) && recoveredAddress == owner,
145           "INVALID_SIGNER"
146:        );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L143-L146

```solidity
File: src/PublicVault.sol

672       require(
673         currentEpoch != 0 &&
674           msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
675:      );

687       require(
688         currentEpoch != 0 &&
689           msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:      );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L672-L675

```solidity
File: src/Vault.sol

65:       require(s.allowList[msg.sender] && receiver == owner());

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/Vault.sol#L65

### [G&#x2011;12]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 2 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit uint8 vaultType
725:        vaultType = uint8(ImplementationType.PublicVault);

/// @audit uint8 vaultType
727:        vaultType = uint8(ImplementationType.PrivateVault);

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L725

### [G&#x2011;13]  Inverting the condition of an `if`-`else`-statement wastes gas
Flipping the `true` and `false` blocks instead saves ***[3 gas](https://gist.github.com/IllIllI000/44da6fbe9d12b9ab21af82f14add56b9)***

*There is 1 instance of this issue:*

```solidity
File: src/CollateralToken.sol

234         if (!exists) {
235           s.CONDUIT = s.CONDUIT_CONTROLLER.createConduit(
236             s.CONDUIT_KEY,
237             address(this)
238           );
239         } else {
240           s.CONDUIT = conduit;
241:        }

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L234-L241

### [G&#x2011;14]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There is 1 instance of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit expensive op on line 111
115:      require(to != address(0), "INVALID_RECIPIENT");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L115

### [G&#x2011;15]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 8 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

143         require(
144           recoveredAddress != address(0) && recoveredAddress == owner,
145           "INVALID_SIGNER"
146:        );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L143-L146

```solidity
File: lib/gpl/src/ERC721.sol

53        require(
54          (owner = _loadERC721Slot()._ownerOf[id]) != address(0),
55          "NOT_MINTED"
56:       );

90        require(
91          msg.sender == owner || s.isApprovedForAll[owner][msg.sender],
92          "NOT_AUTHORIZED"
93:       );

117       require(
118         msg.sender == from ||
119           s.isApprovedForAll[from][msg.sender] ||
120           msg.sender == s.getApproved[id],
121         "NOT_AUTHORIZED"
122:      );

155       require(
156         to.code.length == 0 ||
157           ERC721TokenReceiver(to).onERC721Received(msg.sender, from, id, "") ==
158           ERC721TokenReceiver.onERC721Received.selector,
159         "UNSAFE_RECIPIENT"
160:      );

171       require(
172         to.code.length == 0 ||
173           ERC721TokenReceiver(to).onERC721Received(msg.sender, from, id, data) ==
174           ERC721TokenReceiver.onERC721Received.selector,
175         "UNSAFE_RECIPIENT"
176:      );

240       require(
241         to.code.length == 0 ||
242           ERC721TokenReceiver(to).onERC721Received(
243             msg.sender,
244             address(0),
245             id,
246             ""
247           ) ==
248           ERC721TokenReceiver.onERC721Received.selector,
249         "UNSAFE_RECIPIENT"
250:      );

260       require(
261         to.code.length == 0 ||
262           ERC721TokenReceiver(to).onERC721Received(
263             msg.sender,
264             address(0),
265             id,
266             data
267           ) ==
268           ERC721TokenReceiver.onERC721Received.selector,
269         "UNSAFE_RECIPIENT"
270:      );

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L53-L56

### [G&#x2011;16]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 21 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

252:    function __emergencyPause() external requiresAuth whenNotPaused {

259:    function __emergencyUnpause() external requiresAuth whenPaused {

263:    function fileBatch(File[] calldata files) external requiresAuth {

273:    function file(File calldata incoming) public requiresAuth {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L252

```solidity
File: src/CollateralToken.sol

196:    function fileBatch(File[] calldata files) external requiresAuth {

206:    function file(File calldata incoming) public requiresAuth {

270     function flashAction(
271       IFlashAction receiver,
272       uint256 collateralId,
273       bytes calldata data
274:    ) external onlyOwner(collateralId) {

331     function releaseToAddress(uint256 collateralId, address releaseTo)
332       public
333       releaseCheck(collateralId)
334:      onlyOwner(collateralId)

477     function auctionVault(AuctionVaultParams calldata params)
478       external
479       requiresAuth
480:      returns (OrderParameters memory orderParameters)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L196

```solidity
File: src/LienToken.sol

82:     function file(File calldata incoming) external requiresAuth {

277     function stopLiens(
278       uint256 collateralId,
279       uint256 auctionWindow,
280       Stack[] calldata stack,
281       address liquidator
282:    ) external validateStack(collateralId, stack) requiresAuth {

389     function createLien(ILienToken.LienActionEncumber memory params)
390       external
391       requiresAuth
392       validateStack(params.lien.collateralId, params.stack)
393       returns (
394         uint256 lienId,
395         Stack[] memory newStack,
396:        uint256 lienSlope

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L82

```solidity
File: src/PublicVault.sol

515     function beforePayment(BeforePaymentParams calldata params)
516       external
517:      onlyLienToken

615     function handleBuyoutLien(BuyoutLienParams calldata params)
616       public
617:      onlyLienToken

632     function updateAfterLiquidationPayment(
633       LiquidationPaymentParams calldata params
634:    ) external onlyLienToken {

640     function updateVaultAfterLiquidation(
641       uint256 maxAuctionWindow,
642       AfterLiquidationParams calldata params
643:    ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L515-L517

```solidity
File: src/TransferProxy.sol

28      function tokenTransferFrom(
29        address token,
30        address from,
31        address to,
32        uint256 amount
33:     ) external requiresAuth {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/TransferProxy.sol#L28-L33

```solidity
File: src/WithdrawProxy.sol

163     function withdraw(
164       uint256 assets,
165       address receiver,
166       address owner
167     )
168       public
169       virtual
170       override(ERC4626Cloned, IERC4626)
171       onlyWhenNoActiveAuction
172:      returns (uint256 shares)

184     function redeem(
185       uint256 shares,
186       address receiver,
187       address owner
188     )
189       public
190       virtual
191       override(ERC4626Cloned, IERC4626)
192       onlyWhenNoActiveAuction
193:      returns (uint256 assets)

289     function drain(uint256 amount, address withdrawProxy)
290       public
291       onlyVault
292:      returns (uint256)

306     function handleNewLiquidation(
307       uint256 newLienExpectedValue,
308       uint256 finalAuctionDelta
309:    ) public onlyVault {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L163-L172


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 10 |  1200 |
| [G&#x2011;02] | `<array>.length` should not be looked up in every loop of a `for`-loop | 10 |  30 |
| [G&#x2011;03] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 5 |  25 |
| [G&#x2011;04] | Using `private` rather than `public` for constants, saves gas | 1 |  - |
| [G&#x2011;05] | Use custom errors rather than `revert()`/`require()` strings to save gas | 15 |  - |
| [G&#x2011;06] | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 |  84 |

Total: 45 instances over 6 issues with **1339 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 10 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit commitments - (valid but excluded finding)
490     function commitToLiens(IAstariaRouter.Commitment[] memory commitments)
491       public
492       whenNotPaused
493:      returns (uint256[] memory lienIds, ILienToken.Stack[] memory stack)

/// @audit stack - (valid but excluded finding)
621     function liquidate(ILienToken.Stack[] memory stack, uint8 position)
622       public
623:      returns (OrderParameters memory listedOrder)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L490-L493

```solidity
File: src/ClearingHouse.sol

/// @audit order - (valid but excluded finding)
197:    function validateOrder(Order memory order) external {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L197

```solidity
File: src/CollateralToken.sol

/// @audit params - (valid but excluded finding)
109:    function liquidatorNFTClaim(OrderParameters memory params) external {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L109

```solidity
File: src/LienToken.sol

/// @audit params - (valid but excluded finding)
389     function createLien(ILienToken.LienActionEncumber memory params)
390       external
391       requiresAuth
392       validateStack(params.lien.collateralId, params.stack)
393       returns (
394         uint256 lienId,
395         Stack[] memory newStack,
396:        uint256 lienSlope

/// @audit auctionStack - (valid but excluded finding)
497     function payDebtViaClearingHouse(
498       address token,
499       uint256 collateralId,
500       uint256 payment,
501:      AuctionStack[] memory auctionStack

/// @audit stack - (valid but excluded finding)
706     function getMaxPotentialDebtForCollateral(Stack[] memory stack)
707       public
708       view
709       validateStack(stack[0].lien.collateralId, stack)
710:      returns (uint256 maxPotentialDebt)

/// @audit stack - (valid but excluded finding)
727     function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
728       public
729       view
730       validateStack(stack[0].lien.collateralId, stack)
731:      returns (uint256 maxPotentialDebt)

/// @audit stack - (valid but excluded finding)
742:    function getOwed(Stack memory stack) external view returns (uint88) {

/// @audit stack - (valid but excluded finding)
747     function getOwed(Stack memory stack, uint256 timestamp)
748       external
749       view
750:      returns (uint88)

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L389-L396

### [G&#x2011;02]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 10 instances of this issue:*

```solidity
File: src/AstariaRouter.sol

/// @audit (valid but excluded finding)
265:      for (; i < files.length; ) {

/// @audit (valid but excluded finding)
364:      for (; i < file.length; ) {

/// @audit (valid but excluded finding)
506:      for (; i < commitments.length; ) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/AstariaRouter.sol#L265

```solidity
File: src/ClearingHouse.sol

/// @audit (valid but excluded finding)
96:       for (uint256 i; i < accounts.length; ) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L96

```solidity
File: src/CollateralToken.sol

/// @audit (valid but excluded finding)
198:      for (; i < files.length; ) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/CollateralToken.sol#L198

```solidity
File: src/LienToken.sol

/// @audit (valid but excluded finding)
304:      for (; i < stack.length; ) {

/// @audit (valid but excluded finding)
520:      for (; i < stack.length;) {

/// @audit (valid but excluded finding)
669:      for (uint256 i; i < newStack.length; ) {

/// @audit (valid but excluded finding)
734:      for (; i < stack.length; ) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/LienToken.sol#L304

```solidity
File: src/VaultImplementation.sol

/// @audit (valid but excluded finding)
201:        for (; i < params.allowList.length; ) {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L201

### [G&#x2011;03]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 5 instances of this issue:*

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit (valid but excluded finding)
138:        s._balanceOf[to]++;

/// @audit (valid but excluded finding)
206:        s._balanceOf[to]++;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L138

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
341:        s.currentEpoch++;

/// @audit (valid but excluded finding)
558:        s.epochData[epoch].liensOpenForEpoch++;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L341

```solidity
File: src/VaultImplementation.sol

/// @audit (valid but excluded finding)
69:       s.strategistNonce++;

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L69

### [G&#x2011;04]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There is 1 instance of this issue:*

```solidity
File: src/VaultImplementation.sol

/// @audit (valid but excluded finding)
44      bytes32 public constant STRATEGY_TYPEHASH =
45:       keccak256("StrategyDetails(uint256 nonce,uint256 deadline,bytes32 root)");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/VaultImplementation.sol#L44-L45

### [G&#x2011;05]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 15 instances of this issue:*

```solidity
File: lib/gpl/src/ERC20-Cloned.sol

/// @audit (valid but excluded finding)
115:      require(deadline >= block.timestamp, "PERMIT_DEADLINE_EXPIRED");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC20-Cloned.sol#L115

```solidity
File: lib/gpl/src/ERC4626-Cloned.sol

/// @audit (valid but excluded finding)
25:       require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

/// @audit (valid but excluded finding)
27:       require(shares > minDepositAmount(), "VALUE_TOO_SMALL");

/// @audit (valid but excluded finding)
43:       require(assets > minDepositAmount(), "VALUE_TOO_SMALL");

/// @audit (valid but excluded finding)
94:       require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC4626-Cloned.sol#L25

```solidity
File: lib/gpl/src/ERC721.sol

/// @audit (valid but excluded finding)
60:       require(owner != address(0), "ZERO_ADDRESS");

/// @audit (valid but excluded finding)
113:      require(from == s._ownerOf[id], "WRONG_FROM");

/// @audit (valid but excluded finding)
115:      require(to != address(0), "INVALID_RECIPIENT");

/// @audit (valid but excluded finding)
200:      require(to != address(0), "INVALID_RECIPIENT");

/// @audit (valid but excluded finding)
202:      require(s._ownerOf[id] == address(0), "ALREADY_MINTED");

/// @audit (valid but excluded finding)
219:      require(owner != address(0), "NOT_MINTED");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/lib/gpl/src/ERC721.sol#L60

```solidity
File: src/ClearingHouse.sol

/// @audit (valid but excluded finding)
134:      require(payment >= currentOfferPrice, "not enough funds received");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/ClearingHouse.sol#L134

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
170:      require((assets = previewRedeem(shares)) != 0, "ZERO_ASSETS");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L170

```solidity
File: src/WithdrawProxy.sol

/// @audit (valid but excluded finding)
138:      require(msg.sender == VAULT(), "only vault can mint");

/// @audit (valid but excluded finding)
231:      require(msg.sender == VAULT(), "only vault can call");

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L138

### [G&#x2011;06]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*

```solidity
File: src/PublicVault.sol

/// @audit (valid but excluded finding)
534:    function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {

/// @audit (valid but excluded finding)
562:    function afterPayment(uint256 computedSlope) public onlyLienToken {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/PublicVault.sol#L534

```solidity
File: src/WithdrawProxy.sol

/// @audit (valid but excluded finding)
235:    function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {

/// @audit (valid but excluded finding)
302:    function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {

```
https://github.com/code-423n4/2023-01-astaria/blob/ca120317842c882572516c571b801f3594b8c40c/src/WithdrawProxy.sol#L235
