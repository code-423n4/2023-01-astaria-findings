## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 10 | 1000 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Setting the `constructor` to `payable` | 4 | 52 |
| [GAS&#x2011;3](#GAS&#x2011;3) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 13 | 364 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Empty Blocks Should Be Removed Or Emit Something | 6 | - |
| [GAS&#x2011;5](#GAS&#x2011;5) | `internal` functions only called once can be inlined to save gas | 25 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate | 2 | - |
| [GAS&#x2011;7](#GAS&#x2011;7) | Multiplication/division By Two Should Use Bit Shifting | 1 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Optimize names to save gas | 11 | 242 |
| [GAS&#x2011;9](#GAS&#x2011;9) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 22 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Public Functions To External | 75 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | Splitting `require()` Statements That Use `&&` Saves Gas | 1 | 9 |
| [GAS&#x2011;12](#GAS&#x2011;12) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 2 | - |
| [GAS&#x2011;13](#GAS&#x2011;13) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 67 | - |
| [GAS&#x2011;14](#GAS&#x2011;14) | Using `unchecked` blocks to save gas | 10 | 1360 |
| [GAS&#x2011;15](#GAS&#x2011;15) | Using `storage` instead of `memory` saves gas | 11 | 3850 |
| [GAS&#x2011;16](#GAS&#x2011;16) | Struct packing | 1 | 1000 |

Total: 261 contexts over 16 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
124: s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L124

```solidity
520: abi.encode(listingOrder.parameters)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L520

```solidity
229: abi.encode(newStack)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L229

```solidity
271: if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L271

```solidity
406: abi.encode(newStack)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L406

```solidity
448: newLienId = uint256(keccak256(abi.encode(params.lien)));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L448

```solidity
563: lienId = uint256(keccak256(abi.encode(lien)));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L563

```solidity
698: s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L698

```solidity
155: abi.encode(
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L155

```solidity
184: abi.encode(STRATEGY_TYPEHASH, s.strategistNonce, strategy.deadline, root)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L184








### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
70: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L70

```solidity
76: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L76

```solidity
55: constructor()
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L55

```solidity
24: constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY)
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/TransferProxy.sol#L24





#### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
341: require(msg.sender == s.guardian);
347: require(msg.sender == s.guardian);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L341

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L347



```solidity
199: require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
216: require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
223: require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L199

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L216

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L223



```solidity
241: require(s.allowList[receiver]);
259: require(s.allowList[receiver]);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L241

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L259



```solidity
78: require(msg.sender == owner());
96: require(msg.sender == owner());
105: require(msg.sender == owner());
114: require(msg.sender == owner());
147: require(msg.sender == owner());
211: require(msg.sender == owner());

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L78

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L96

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L105

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L114

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L147

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L211








### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
87: function _beforeFallback() internal virtual {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol#L87

```solidity
104: function setApprovalForAll(address operator, bool approved) external {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L104

```solidity
186: ) public {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L186

```solidity
272: ) internal virtual {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L272

```solidity
272: {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L272

```solidity
277: {}
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L277






### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
761: function _executeCommitment

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L761

```solidity
788: function _transferAndDepositAssetIfAble

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L788

```solidity
20: function _getBeacon

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol#L20

```solidity
29: function _delegate

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/BeaconProxy.sol#L29

```solidity
114: function _execute

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L114

```solidity
425: function _generateValidOrderParameters

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L425

```solidity
502: function _listUnderlyingOnSeaport

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L502

```solidity
541: function _settleAuction

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L541

```solidity
122: function _buyoutLien

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L122

```solidity
233: function _replaceStackAtPositionWithNewLien

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L233

```solidity
292: function _stopLiens

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L292

```solidity
459: function _appendStack

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L459

```solidity
512: function _payDebt

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L512

```solidity
623: function _paymentAH

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L623

```solidity
663: function _makePayment

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L663

```solidity
715: function _getMaxPotentialDebtForCollateralUpToNPositions

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L715

```solidity
855: function _removeStackPosition

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L855

```solidity
911: function _setPayee

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L911

```solidity
271: function computeDomainSeparator

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L271

```solidity
439: function _afterCommitToLien

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L439

```solidity
556: function _increaseOpenLiens

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L556

```solidity
597: function _handleStrategistInterestReward

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L597

```solidity
268: function _afterCommitToLien

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L268

```solidity
379: function _requestLienAndIssuePayout

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L379

```solidity
397: function _handleProtocolFee

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L397






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

#### <ins>Proof Of Concept</ins>

i.e. The following can be changed to the following struct:

```solidity
60: mapping(address => bool) flashEnabled;
64: mapping(address => address) securityHooks;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L60
https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L64

```solidity
    struct sampleStructName {
        bool flashEnabled;
        address securityHooks;
    }
```



### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
512: totalBorrowed += payout;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L512

```solidity
160: potentialDebt += _getOwed(
210: maxPotentialDebt += _getOwed(newStack[i], newStack[i].point.end);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L160

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L210



```solidity
480: potentialDebt += _getOwed(newStack[j], newStack[j].point.end);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L480

```solidity
524: totalSpent += spent;
525: payment -= spent;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L524-L525

```solidity
679: totalCapitalAvailable -= spent;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L679

```solidity
720: maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L720

```solidity
735: maxPotentialDebt += _getOwed(stack[i], end);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L735

```solidity
830: stack.point.amount -= amount.safeCastTo88();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L830

```solidity
174: es.balanceOf[owner] -= shares;
179: es.balanceOf[address(this)] += shares;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L174

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L179



```solidity
380: s.withdrawReserve -= withdrawBalance.safeCastTo88();
405: s.withdrawReserve -= drainBalance.safeCastTo88();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L380

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L405



```solidity
565: s.slope += computedSlope.safeCastTo48();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L565

```solidity
583: s.yIntercept += assets.safeCastTo88();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L583

```solidity
606: s.strategistUnclaimedShares += feeInShares;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L606

```solidity
624: s.yIntercept += params.increaseYIntercept.safeCastTo88();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L624

```solidity
404: amount -= fee;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/VaultImplementation.sol#L404

```solidity
237: s.withdrawReserveReceived += amount;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L237

```solidity
277: balance -= transferAmount;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L277

```solidity
313: s.expected += newLienExpectedValue.safeCastTo88();

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L313





### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>

Small sample of examples: 

```solidity
function redeemFutureEpoch(
    IPublicVault vault,
    uint256 shares,
    address receiver,
    uint64 epoch
  ) public virtual validVault(address(vault)) returns (uint256 assets) {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L187

```solidity
function pullToken(
    address token,
    uint256 amount,
    address recipient
  ) public payable override {
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L203

This applies to all in-scope contracts that use the public modifier rather than the external modifier that are only called externally.






### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(s.allowList[msg.sender] && receiver == owner());` use:

```
	require(s.allowList[msg.sender]);
	require(receiver == owner());
```

#### <ins>Proof Of Concept</ins>

```solidity
65: require(s.allowList[msg.sender] && receiver == owner());
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/Vault.sol#L65





### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
302: s.auctionData[collateralId].liquidator = liquidator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L302

```solidity
876: stack[position].lien.collateralId,
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L876








### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
442: uint8 nlrType = uint8(_sliceUint(commitment.lienRequest.nlrDetails, 0));
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L442

```solidity
722: uint8 vaultType;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/AstariaRouter.sol#L722

```solidity
309: uint88 owed = _getOwed(stack[i], block.timestamp);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L309

```solidity
803: uint64 end = stack.point.end;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L803

```solidity
356: interfaceId == type(IERC165).interfaceId;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L356

```solidity
449: uint48 newSlope = s.slope + lienSlope.safeCastTo48();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L449

```solidity
453: uint64 epoch = getLienEpoch(lienEnd);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L453

```solidity
622: uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L622

```solidity
605: uint88 feeInShares = convertToShares(fee).safeCastTo88();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L605

```solidity
654: uint64 lienEpoch = getLienEpoch(params.lienEnd);
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L654

```solidity
686: uint64 currentEpoch = s.currentEpoch;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L686

```solidity
53: uint88 withdrawRatio;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L53

```solidity
54: uint88 expected;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L54

```solidity
55: uint40 finalAuctionEnd;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L55

```solidity
314: uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/WithdrawProxy.sol#L314

```solidity
58: uint32 auctionWindow;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L58

```solidity
59: uint32 auctionWindowBuffer;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L59

```solidity
60: uint32 liquidationFeeNumerator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L60

```solidity
61: uint32 liquidationFeeDenominator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L61

```solidity
62: uint32 maxEpochLength;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L62

```solidity
63: uint32 minEpochLength;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L63

```solidity
64: uint32 protocolFeeNumerator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L64

```solidity
65: uint32 protocolFeeDenominator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L65

```solidity
73: uint88 maxInterestRate;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L73

```solidity
74: uint32 minInterestBPS;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L74

```solidity
78: uint32 buyoutFeeNumerator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L78

```solidity
79: uint32 buyoutFeeDenominator;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L79

```solidity
80: uint32 minDurationIncrease;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L80

```solidity
102: uint8 version;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L102

```solidity
118: uint8 v;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IAstariaRouter.sol#L118

```solidity
71: uint56 maxDuration;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ICollateralToken.sol#L71

```solidity
37: uint8 maxLiens;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L37

```solidity
61: uint8 collateralType;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L61

```solidity
70: uint88 amount;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L70

```solidity
71: uint40 last;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L71

```solidity
232: uint40 end;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L232

```solidity
89: uint8 position;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L89

```solidity
231: uint88 amountOwed;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L231

```solidity
236: uint88 startAmount;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L236

```solidity
237: uint88 endAmount;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L237

```solidity
238: uint48 startTime;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L238

```solidity
239: uint48 endTime;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\ILienToken.sol#L239

```solidity
21: uint64 liensOpenForEpoch;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L21

```solidity
26: uint88 yIntercept;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L26

```solidity
27: uint48 slope;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L27

```solidity
28: uint40 last;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L28

```solidity
29: uint64 currentEpoch;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L29

```solidity
30: uint88 withdrawReserve;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L30

```solidity
31: uint88 liquidationWithdrawRatio;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L31

```solidity
32: uint88 strategistUnclaimedShares;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L32

```solidity
51: uint40 lienEnd;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IPublicVault.sol#L51

```solidity
76: uint24 fee;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L76

```solidity
77: int24 tickLower;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L77

```solidity
78: int24 tickUpper;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L78

```solidity
135: uint128 liquidity;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L135

```solidity
157: uint128 amount0Max;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L157

```solidity
158: uint128 amount1Max;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IV3PositionManager.sol#L158

```solidity
44: uint88 depositCap;
```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/interfaces\IVaultImplementation.sol#L44






### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
150: payment - liquidatorPayment
156: payment - liquidatorPayment

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L150

https://github.com/code-423n4/2023-01-astaria/tree/main/src/ClearingHouse.sol#L156



```solidity
861: newStack = new ILienToken.Stack[](length - 1);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L861



```solidity
163: es.allowance[owner][msg.sender] = allowed - shares;

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L163

```solidity
674: msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
689: msg.sender == s.epochData[currentEpoch - 1].withdrawProxy

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L674

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L689



```solidity
674: msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
689: msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
691: _setYIntercept(s, s.yIntercept - amount);

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L674

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L689

https://github.com/code-423n4/2023-01-astaria/tree/main/src/PublicVault.sol#L691






### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
137: Asset memory underlying = s.idToUnderlying[collateralId];
341: Asset memory underlying = s.idToUnderlying[collateralId];
434: Asset memory underlying = s.idToUnderlying[collateralId];

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L137

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L341

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L434



```solidity
137: Asset memory underlying = s.idToUnderlying[collateralId];
341: Asset memory underlying = s.idToUnderlying[collateralId];
434: Asset memory underlying = s.idToUnderlying[collateralId];

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L137

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L341

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L434



```solidity
137: Asset memory underlying = s.idToUnderlying[collateralId];
341: Asset memory underlying = s.idToUnderlying[collateralId];
434: Asset memory underlying = s.idToUnderlying[collateralId];

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L137

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L341

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L434



```solidity
562: Asset memory incomingAsset = s.idToUnderlying[collateralId];

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/CollateralToken.sol#L562

```solidity
797: Stack memory stack = activeStack[position];

```

https://github.com/code-423n4/2023-01-astaria/tree/main/src/LienToken.sol#L797


### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Struct packing

In `IFlashAction.sol`, `AuctionVaultParams`'s maxDuration is unlikely to ever be used with `uint256.max` value. This can be changed to at least `uint96` to save 1 storage slot.

```solidity
  struct AuctionVaultParams {
    address settlementToken;
    uint256 collateralId;
    uint256 maxDuration;
    uint256 startingPrice;
    uint256 endingPrice;
  }
```

Can be changed to:
```solidity
  struct AuctionVaultParams {
    address settlementToken;
    uint96 maxDuration;
    uint256 collateralId;
    uint256 startingPrice;
    uint256 endingPrice;
  }
```
