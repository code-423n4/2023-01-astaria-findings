## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| L-01 | Remove unchecked code block for calculations with arbitrary call params | 3 |
| L-02 | Move owner checks to a modifier | 7 |
| L-03 | Use helper functions instead of direct _getArgAddress / _get.. from the Clone contract | 6 |
| L-04 | Throw an error instead of returning missleading default values or an empty code-block in ClearingHouse | 5 |
| L-05 | Remove conditions in CollateralToken that result in unreachable code | 2 |
| L-06 | Don't redeclare a already used variable name in a function | 1 |
| L-07 | Throw an error instead of just returning the function early | 1 |
| L-08 | Add a != address(0) check on error prone places | 3 |
| L-09 | Loop in LienToken._makePayment has a condition that can break the payment | 1 |

Total: 29 instances over 9 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| NC-01 | Consider a not so strict versioning for your main interfaces | 22 |
| NC-02 | Defined return value not used | 4 |
| NC-03 | Don't import a Contract and overwrite the function with the same implementation | 1 |
| NC-04 | Remove redundant ABIEncoderV2 | 1 |
| NC-05 | Resolve compiler warnings | 40 |
| NC-06 | Missing License | 4 |
| NC-07 | Unnecessarily imports | 16 |

Total: 88 instances over 7 issues

### Informational
| |Issue|Instances|
|-|:-|:-:|
| I-01 | Missmatch description / comment to logic | 3 |
| I-02 | Add missing @inheritdoc NatSpec in implementation contracts | 9 |
| I-03 | Long lines | 6 |
| I-04 | Typos | 3 |
| I-05 | Add missing documentation | 66 |

Total: 87 instances over 5 issues

---

## Low Risk Issues

### L-01 Remove unchecked code block for calculations with arbitrary call params
Don't use unchecked code blocks if the values are coming from another contract via an arbitrary call. Especially if they are not checked properly in the calling contract before.

```solidity
File: src/PublicVault.sol
517:   function beforePayment(BeforePaymentParams calldata params)
518:     external
519:     onlyLienToken
520:   {
521:     VaultData storage s = _loadStorageSlot();
522:     _accrue(s);
523: 
524:     unchecked {
525:       uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
526:       _setSlope(s, newSlope);
527:     }

File: src/PublicVault.sol
617:   function handleBuyoutLien(BuyoutLienParams calldata params)
618:     public
619:     onlyLienToken
620:   {
621:     VaultData storage s = _loadStorageSlot();
622: 
623:     unchecked {
624:       uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
625:       _setSlope(s, newSlope);
626:       s.yIntercept += params.increaseYIntercept.safeCastTo88();
627:       s.last = block.timestamp.safeCastTo40();
628:     }

File: src/PublicVault.sol
642:   function updateVaultAfterLiquidation(
643:     uint256 maxAuctionWindow,
644:     AfterLiquidationParams calldata params
645:   ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
646:     VaultData storage s = _loadStorageSlot();
647: 
648:     _accrue(s);
649:     unchecked {
650:       _setSlope(s, s.slope - params.lienSlope.safeCastTo48());
651:     }

```

### L-02 Move owner checks to a modifier
It's better to use a modifier for simple owner checks for an easier inspection of functions. In a function it can be forgotten and with a modifier it's easier to see how the function is protected.

```solidity
File: src/PublicVault.sol
510:     require(msg.sender == owner()); //owner is "strategist"

File: src/VaultImplementation.sol
79:   function modifyDepositCap(uint256 newCap) external {
80:     require(msg.sender == owner()); //owner is "strategist"

File: src/VaultImplementation.sol
97:   function modifyAllowList(address depositor, bool enabled) external virtual {
98:     require(msg.sender == owner()); //owner is "strategist" 

File: src/VaultImplementation.sol
106:   function disableAllowList() external virtual {
107:     require(msg.sender == owner()); //owner is "strategist" 

File: src/VaultImplementation.sol
115:   function enableAllowList() external virtual {
116:     require(msg.sender == owner()); //owner is "strategist"

File: src/VaultImplementation.sol
148:   function shutdown() external {
149:     require(msg.sender == owner()); //owner is "strategist"

File: src/VaultImplementation.sol
212:   function setDelegate(address delegate_) external {
213:     require(msg.sender == owner()); //owner is "strategist"

```

### L-03 Use helper functions instead of direct _getArgAddress / _get.. from the Clone contract
You should use the implemented helper functions instead of using the _getXXX functions from the Clone contract. It can be very error prone as if you ever change anything you need to check it everywhere and this can lead to unexpected behaviour.

```solidity
File: src/ClearingHouse.sol
69:   function setAuctionData(ILienToken.AuctionData calldata auctionData)
70:     external
71:   {
72:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
=> use ROUTER() 

File: src/ClearingHouse.sol
117:   function _execute(
118:     address tokenContract, // collateral token sending the fake nft
119:     address to, // buyer
120:     uint256 encodedMetaData, //retrieve token address from the encoded data
121:     uint256 // space to encode whatever is needed,
122:   ) internal {
123:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
=> use ROUTER() 

File: src/ClearingHouse.sol
137:     require(payment >= currentOfferPrice, "not enough funds received");
138: 
139:     uint256 collateralId = _getArgUint256(21);
=> use COLLATERAL_ID()

File: src/ClearingHouse.sol
200:   function validateOrder(Order memory order) external {
201:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0)); 
=> use ROUTER() 

File: src/ClearingHouse.sol
213:   function transferUnderlying(
214:     address tokenContract,
215:     uint256 tokenId,
216:     address target
217:   ) external {
218:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0)); 
=> use ROUTER() 

File: src/ClearingHouse.sol
223:   function settleLiquidatorNFTClaim() external {
224:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0)); 
=> use ROUTER()
```

### L-04 Throw an error instead of returning missleading default values or an empty code-block in ClearingHouse
In ClearingHouse you return default values for functions or have empty code blocks just to fulfill the Interface. Consider throwing an error in the implementation instead of an empty code block or the missleading return of default values.

```solidity
File: src/ClearingHouse.sol
85:   function balanceOf(address account, uint256 id)
86:     external
87:     view
88:     returns (uint256)
89:   {
90:     return type(uint256).max;
91:   }

File: src/ClearingHouse.sol
093:   function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
094:     external
095:     view
096:     returns (uint256[] memory output)
097:   {
098:     output = new uint256[](accounts.length);
099:     for (uint256 i; i < accounts.length; ) {
100:       output[i] = type(uint256).max;
101:       unchecked {
102:         ++i;
103:       }
104:     }
105:   }

File: src/ClearingHouse.sol
107:   function setApprovalForAll(address operator, bool approved) external {} 

File: src/ClearingHouse.sol
109:   function isApprovedForAll(address account, address operator)
110:     external
111:     view
112:     returns (bool)
113:   {
114:     return true;
115:   }

File: src/ClearingHouse.sol
183:   function safeBatchTransferFrom(
184:     address from,
185:     address to,
186:     uint256[] calldata ids,
187:     uint256[] calldata amounts,
188:     bytes calldata data
189:   ) public {}

```

### L-05 Remove conditions in CollateralToken that result in unreachable code
If it's impossible for a condition to succeed, consider removing it as it's an unneeded complexity.

```solidity
File: src/CollateralToken.sol
118:     address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
119:     if (
120:       s.collateralIdToAuction[collateralId] == bytes32(0) ||
121:       liquidator == address(0)
122:     ) {
123:       //revert no auction
124:       revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
125:     }
=> LienToken.getAuctionLiquidator reverts if liquidator == address(0), so you can remove the liquidator == address(0) check.

File: src/CollateralToken.sol
334:   function releaseToAddress(uint256 collateralId, address releaseTo)
335:     public
336:     releaseCheck(collateralId)
337:     onlyOwner(collateralId)
338:   {
339:     CollateralStorage storage s = _loadCollateralSlot();
340: 
341:     if (msg.sender != ownerOf(collateralId)) {
342:       revert InvalidSender();
343:     }
=> function has the onlyOwner modifier that has already the check for (msg.sender == ownerOf(collateralId)), so you can remove it here.
```

### L-06 Don't redeclare a already used variable name in a function
You should not redeclare the same variable name inside a function and shadow it. Just use the already defined variable, as it's the same value you define inside.

```solidity
File: src/PublicVault.sol
399:       address currentWithdrawProxy = s
400:         .epochData[s.currentEpoch - 1]
401:         .withdrawProxy;
=> already declared in the beginning of the function via
File: src/PublicVault.sol
368:     address currentWithdrawProxy = s
369:       .epochData[s.currentEpoch - 1]
370:       .withdrawProxy;
```

### L-07 Throw an error instead of just returning the function early
In PublicVault you return the transferWithdrawReserve function early. It's better to throw an error here so so the calling function / user / system knows that it was not successful and should try it again later.

```solidity
File: src/PublicVault.sol
361:   function transferWithdrawReserve() public {
362:     VaultData storage s = _loadStorageSlot();
363: 
364:     if (s.currentEpoch == uint64(0)) {
365:       return;
366:     }
```

### L-08 Add a != address(0) check on error prone places
To not send tokens to the zero-address or update an important state variable to the zero-address you should check that the address is not the zero-address.

```solidity
File: src/PublicVault.sol
140:   function redeemFutureEpoch(
141:     uint256 shares,
142:     address receiver,
143:     address owner,
144:     uint64 epoch
145:   ) public virtual returns (uint256 assets) {
146:     return
147:       _redeemFutureEpoch(_loadStorageSlot(), shares, receiver, owner, epoch);
148:   }
=> check that receiver != address(0) as it will get the tokens transfered.

File: src/LienToken.sol
91:       s.COLLATERAL_TOKEN = ICollateralToken(abi.decode(data, (address)));

File: src/LienToken.sol
94:       s.ASTARIA_ROUTER = IAstariaRouter(abi.decode(data, (address)));
```

### L-09 Loop in LienToken._makePayment has a condition that can break the payment
The loop in _makePayment function in LienToken has a condition that can lead to unexpected behaviour.

```solidity
File: src/LienToken.sol
685:       if (newStack.length == oldLength) {
686:         unchecked {
687:           ++i;
688:         }
689:       }
```

This condition should never be true and should be unreachable. If it will ever resolve, the ++i can lead to an unexpected behaviour.

The _payment returns spent = totalCapitalAvailable or it removes one stack. It then breaks (if (totalCapitalAvailable == 0) break) or continues and tries to pay the new current 0 index.
If it will ever ++i it will pay the wrong index 1 instead of 0.

---

## Non-critical Issues

### NC-01 Consider a not so strict versioning for your main interfaces
At the moment your main interfaces under /src/interfaces are only compatible with solidity 0.8.17. Consider a more open Version like ^0.8.0 so other projects can import your original interfaces and dont need to rewrite or change them if they use another version than 0.8.17.

```solidity
All under
File: src/interfaces/xxxx.sol
```

### NC-02 Defined return value not used
If you're not using the variable name in the function declaration you should remove it.

```solidity
File: src/WithdrawProxy.sol
139:     returns (uint256 assets)
=> var assets not used in implementation

File: src/WithdrawProxy.sol
196:     returns (uint256 assets)
=> var assets not used in implementation

File: src/VaultImplementation.sol
355:   function _timeToSecondEndIfPublic()
356:     internal
357:     view
358:     virtual
359:     returns (uint256 timeToSecondEpochEnd)
=> var timeToSecondEpochEnd not used

File: src/PublicVault.sol
715:   function _timeToSecondEndIfPublic()
716:     internal
717:     view
718:     override
719:     returns (uint256 timeToSecondEpochEnd)
720:   {
721:     return timeToEpochEnd() + EPOCH_LENGTH();
722:   }
```

### NC-03 Don't import a Contract and overwrite the function with the same implementation
If you import and extend a Contract you should not overwrite the function with the exact same implementation. Consider removing the import and extension, or remove the implementation in the contract.

```solidity
File: src/VaultImplementation.sol
124:   function onERC721Received(
125:     address, // operator_
126:     address, // from_
127:     uint256, // tokenId_
128:     bytes calldata // data_
129:   ) external pure override returns (bytes4) {
130:     return ERC721TokenReceiver.onERC721Received.selector;
131:   }
=> VaultImplementation is ERC721TokenReceiver and onERC721Received is defined in ERC721TokenReceiver. No change in VaultImplementation.
```

### NC-04 Remove redundant ABIEncoderV2
Since solidity 0.8.0 the ABIEncoderV2 is the default and it's not needed anymore to import it.
https://docs.soliditylang.org/en/latest/080-breaking-changes.html#how-to-update-your-code
> Optionally remove pragma experimental ABIEncoderV2 or pragma abicoder v2 since it is redundant.

```solidity
File: src/LienToken.sol
14: pragma solidity =0.8.17;
15: 
16: pragma experimental ABIEncoderV2;

```

### NC-05 Resolve compiler warnings
At the moment there are some compiler warnings that should be resolved.

```solidity
warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/ClearingHouse.sol:85:22:
   |
85 |   function balanceOf(address account, uint256 id)
   |                      ^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/ClearingHouse.sol:85:39:
   |
85 |   function balanceOf(address account, uint256 id)
   |                                       ^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/ClearingHouse.sol:93:56:
   |
93 |   function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
   |                                                        ^^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:109:29:
    |
109 |   function isApprovedForAll(address account, address operator)
    |                             ^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:109:46:
    |
109 |   function isApprovedForAll(address account, address operator)
    |                                              ^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:118:5:
    |
118 |     address tokenContract, // collateral token sending the fake nft
    |     ^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:119:5:
    |
119 |     address to, // buyer
    |     ^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/ClearingHouse.sol:142:5:
    |
142 |     ILienToken.AuctionStack[] storage stack = s.auctionStack.stack;
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:177:5:
    |
177 |     bytes calldata data //empty from seaport
    |     ^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:192:5:
    |
192 |     address operator_,
    |     ^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:193:5:
    |
193 |     address from_,
    |     ^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:194:5:
    |
194 |     uint256 tokenId_,
    |     ^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/ClearingHouse.sol:195:5:
    |
195 |     bytes calldata data_
    |     ^^^^^^^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/LienToken.sol:635:5:
    |
635 |     uint256 end = stack[position].end;
    |     ^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/LienToken.sol:778:34:
    |
778 |   function _getRemainingInterest(LienStorage storage s, Stack memory stack)
    |                                  ^^^^^^^^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/CollateralToken.sol:141:5:
    |
141 |     address tokenContract = underlying.tokenContract;
    |     ^^^^^^^^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/CollateralToken.sol:142:5:
    |
142 |     uint256 tokenId = underlying.tokenId;
    |     ^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/CollateralToken.sol:162:5:
    |
162 |     address caller,
    |     ^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/CollateralToken.sol:163:5:
    |
163 |     address offerer,
    |     ^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/CollateralToken.sol:176:5:
    |
176 |     address caller,
    |     ^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/CollateralToken.sol:178:5:
    |
178 |     bytes32[] calldata priorOrderHashes,
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/CollateralToken.sol:179:5:
    |
179 |     CriteriaResolver[] calldata criteriaResolvers
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/CollateralToken.sol:345:5:
    |
345 |     address tokenContract = underlying.tokenContract;
    |     ^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/WithdrawProxy.sol:146:20:
    |
146 |   function deposit(uint256 assets, address receiver)
    |                    ^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/WithdrawProxy.sol:146:36:
    |
146 |   function deposit(uint256 assets, address receiver)
    |                                    ^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/WithdrawProxy.sol:150:14:
    |
150 |     returns (uint256 shares)
    |              ^^^^^^^^^^^^^^

warning[2072]: Warning: Unused local variable.
   --> src/PublicVault.sol:264:5:
    |
264 |     uint256 assets = totalAssets();
    |     ^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/PublicVault.sol:415:32:
    |
415 |   function _beforeCommitToLien(IAstariaRouter.Commitment calldata params)
    |                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/PublicVault.sol:577:41:
    |
577 |   function afterDeposit(uint256 assets, uint256 shares)
    |                                         ^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
  --> src/security/V3SecurityHook.sol:25:21:
   |
25 |   function getState(address tokenContract, uint256 tokenId)
   |                     ^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/test/TestHelpers.t.sol:372:5:
    |
372 |     uint256 royaltyPercentage,
    |     ^^^^^^^^^^^^^^^^^^^^^^^^^

warning[5667]: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
   --> src/test/TestHelpers.t.sol:810:5:
    |
810 |     address strategist,
    |     ^^^^^^^^^^^^^^^^^^

warning[2018]: Warning: Function state mutability can be restricted to pure
  --> src/ClearingHouse.sol:81:3:
   |
81 |   function supportsInterface(bytes4 interfaceId) external view returns (bool) {
   |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to pure
  --> src/ClearingHouse.sol:85:3:
   |
85 |   function balanceOf(address account, uint256 id)
   |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to pure
  --> src/ClearingHouse.sol:93:3:
   |
93 |   function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
   |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to pure
   --> src/ClearingHouse.sol:109:3:
    |
109 |   function isApprovedForAll(address account, address operator)
    |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to pure
   --> src/ClearingHouse.sol:191:3:
    |
191 |   function onERC721Received(
    |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to pure
  --> src/AuthInitializable.sol:34:3:
   |
34 |   function _getAuthSlot() internal view returns (AuthStorage storage s) {
   |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to view
   --> src/LienToken.sol:462:3:
    |
462 |   function _appendStack(
    |   ^ (Relevant source part starts here and spans across multiple lines).

warning[2018]: Warning: Function state mutability can be restricted to view
   --> src/CollateralToken.sol:428:3:
    |
428 |   function _generateValidOrderParameters(
    |   ^ (Relevant source part starts here and spans across multiple lines).

```

### NC-06 Missing License
There are 4 interfaces imported without a license, consider update them to not use code without a license.

```solidity
File: IERC20Metadata.sol
1: pragma solidity =0.8.17;

File: IERC721.sol
1: pragma solidity =0.8.17;

File: IV3PositionManager.sol
1: pragma solidity =0.8.17;

File: IWETH9.sol
1: pragma solidity ^0.8.13;
```

### NC-07 Unnecessarily imports
Some contracts have unnecessary imports that are never used and can be removed.

```solidity
File: AstariaVaultBase.sol
18: import {IERC4626} from "core/interfaces/IERC4626.sol";

File: AstariaVaultBase.sol
21: import {IRouterBase} from "core/interfaces/IRouterBase.sol";

File: ClearingHouse.sol
17: import {WETH} from "solmate/tokens/WETH.sol";

File: ClearingHouse.sol
24: import {
25:   ConduitControllerInterface
26: } from "seaport/interfaces/ConduitControllerInterface.sol";

File: CollateralToken.sol
33: import {VaultImplementation} from "core/VaultImplementation.sol";

File: CollateralToken.sol
53:   OrderComponents,

File: CollateralToken.sol
59: import {SeaportInterface} from "seaport/interfaces/SeaportInterface.sol";

File: IAstariaRouter.sol
16: import {IERC721} from "core/interfaces/IERC721.sol";

File: IAstariaRouter.sol
25: import {IERC4626RouterBase} from "gpl/interfaces/IERC4626RouterBase.sol";

File: IAstariaVaultBase.sol
17: import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";

File: ICollateralToken.sol
27: import {IERC1155} from "core/interfaces/IERC1155.sol";

File: IPublicVault.sol
16: import {IERC165} from "core/interfaces/IERC165.sol";

File: IWithdrawProxy.sol
18: import {IAstariaRouter} from "core/interfaces/IAstariaRouter.sol";

File: LienToken.sol
34: import {VaultImplementation} from "./VaultImplementation.sol";

File: PublicVault.sol
24: import {IERC4626} from "core/interfaces/IERC4626.sol";

File: VaultImplementation.sol
25: import {IPublicVault} from "core/interfaces/IPublicVault.sol";

```

---

## Informational

### I-01 Missmatch description / comment to logic
If there is a missmatch between the NatSpec documentation or a comment to the implementation, you should change the documentation / comment or consider an update of the logic.

```solidity
File: src/AstariaVaultBase.sol
38:   function owner() public pure returns (address) {
39:     return _getArgAddress(21); //ends at 44
40:   }
=> 21 != 44

File: ILienToken.sol
137:   /**
138:    * @notice Removes all liens for a given CollateralToken.
139:    * @param stack The Lien stack
140:    * @return the amount owed in uint192 at the current block.timestamp
141:    */
142:   function getOwed(Stack calldata stack) external view returns (uint88);
=> return is not uint192

File: ILienToken.sol
144:   /**
145:    * @notice Removes all liens for a given CollateralToken.
146:    * @param stack The Lien
147:    * @param timestamp the timestamp you want to inquire about
148:    * @return the amount owed in uint192
149:    */
150:   function getOwed(Stack calldata stack, uint256 timestamp)
151:     external
152:     view
153:     returns (uint88);
=> return is not uint192

```

### I-02 Add missing @inheritdoc NatSpec in implementation contracts
At the moment all the documentation are done in the Interfaces. Most of the implementation contracts miss the @inheritdoc NatSpec comment. Try to add them to the functions for a better developer experience. Consider at least the following contracts to add them.

```solidity
File: src/LienToken.sol
File: src/CollateralToken.sol
File: src/Vault.sol
File: src/PublicVault.sol
File: src/VaultImplementation.sol
File: src/AstariaVaultBase.sol
File: src/WithdrawProxy.sol
File: src/WithdrawVaultBase.sol
File: src/AstariaRouter.sol
```


### I-03 Long lines
Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

```solidity
File: IERC4626RouterBase.sol
15:  NOTE the router is capable of pulling any approved token from your wallet. This is only possible when your address is msg.sender, but regardless be careful when interacting with the router or ERC4626 Vaults.

File: IWithdrawProxy.sol
27:    * @notice Called at epoch boundary, computes the ratio between the funds of withdrawing liquidity providers and the balance of the underlying PublicVault so that claim() proportionally pays optimized-out to all parties.

File: IWithdrawProxy.sol
35:    * @param finalAuctionDelta The timestamp by which the auction being added is guaranteed to end. As new auctions are added to the WithdrawProxy, this value will strictly increase as all auctions have the same maximum duration.

File: LienToken.sol
42:  * @notice This contract handles the creation, payments, buyouts, and liquidations of tokenized NFT-collateralized debt (liens). Vaults which originate loans against supported collateral are issued a LienToken representing the right to loan repayments and auctioned funds on liquidation.

File: WithdrawProxy.sol
54:     uint88 expected; // The sum of the remaining debt (amountOwed) accrued against the NFT at the timestamp when it is liquidated. yIntercept (virtual assets) of a PublicVault are not modified on liquidation, only once an auction is completed.

File: WithdrawProxy.sol
56:     uint256 withdrawReserveReceived; // amount received from PublicVault. The WETH balance of this contract - withdrawReserveReceived = amount received from liquidations.
```

### I-04 Typos
There are some typos.

```solidity
File: AuthInitializable.sol
72:     Authority auth = s.authority; // Memoizing authority saves us a warm SLOAD, around 100 gas.
=> Memorizing

File: PublicVault.sol
307:     // reset liquidationWithdrawRatio to prepare for re calcualtion
=> calculation

File: Vault.sol
90:     //invalid action private vautls can only be the owner or strategist
=> vaults
```

### I-05 Add missing documentation
The contracts are very well documented and clear to read. Still it misses some documentation that could be added for a even better developer experience. Functions missing @param (or wrong param mentioned) / @return / @notice or is missing complete NatSpec.

Only protocol Interfaces are mentioned.

```solidity
File: IAstariaRouter.sol
134:   function validateCommitment(
135:     IAstariaRouter.Commitment calldata commitment,
136:     uint256 timeToSecondEpochEnd
=> missing timeToSecondEpochEnd

File: IAstariaRouter.sol
149:   function newPublicVault(
150:     uint256 epochLength,
151:     address delegate,
152:     address underlying,
153:     uint256 vaultFee,
154:     bool allowListEnabled,
155:     address[] calldata allowList,
156:     uint256 depositCap
157:   ) external returns (address);
=> missing return

File: IAstariaRouter.sol
172:   function feeTo() external returns (address);
=> missing return

File: IAstariaRouter.sol
179:   function commitToLiens(Commitment[] memory commitments)
180:     external
181:     returns (uint256[] memory, ILienToken.Stack[] memory);
=> missing second return

File: IAstariaRouter.sol
188:   function requestLienPosition(
189:     IAstariaRouter.Commitment calldata params,
190:     address recipient
191:   )
192:     external
193:     returns (
194:       uint256,
195:       ILienToken.Stack[] memory,
196:       uint256
197:     );
=> missing recipient and second + third return

File: IAstariaRouter.sol
199:   function LIEN_TOKEN() external view returns (ILienToken);
200: 
201:   function TRANSFER_PROXY() external view returns (ITransferProxy);
202: 
203:   function BEACON_PROXY_IMPLEMENTATION() external view returns (address);
204: 
205:   function COLLATERAL_TOKEN() external view returns (ICollateralToken);
=> missing returns and notic

File: IAstariaRouter.sol
211:   function getAuctionWindow(bool includeBuffer) external view returns (uint256);
=> missing returns

File: IAstariaRouter.sol
216:   function getProtocolFee(uint256) external view returns (uint256);
=> missing param and return

File: IAstariaRouter.sol
221:   function getBuyoutFee(uint256) external view returns (uint256);
=> missing param and return

File: IAstariaRouter.sol
226:   function getLiquidatorFee(uint256) external view returns (uint256);
=> missing param and return

File: IAstariaRouter.sol
241:   function canLiquidate(ILienToken.Stack calldata) external view returns (bool);
=> missing param and return

File: IAstariaRouter.sol
278:   function getImpl(uint8 implType) external view returns (address impl);
=> missing param

File: IAstariaRouter.sol
288:   function isValidRefinance(
289:     ILienToken.Lien calldata newLien,
290:     uint8 position,
291:     ILienToken.Stack[] calldata stack
292:   ) external view returns (bool);
293: 
=> missing return

File: IAstariaVaultBase.sol
21:   function owner() external view returns (address);
22: 
23:   function asset() external view returns (address);
24: 
25:   function COLLATERAL_TOKEN() external view returns (ICollateralToken);
26: 
27:   function START() external view returns (uint256);
28: 
29:   function EPOCH_LENGTH() external view returns (uint256);
30: 
31:   function VAULT_FEE() external view returns (uint256);
=> missing notice and return

File: IBeacon.sol
22:   function getImpl(uint8) external view returns (address);
=> wrong NatSpec

File: ICollateralToken.sol
111:   function securityHooks(address) external view returns (address);
112: 
113:   function getConduit() external view returns (address);
114: 
115:   function getConduitKey() external view returns (bytes32);
116: 
117:   function getClearingHouse(uint256) external view returns (ClearingHouse);
=> missing NatSpec

File: ICollateralToken.sol
131:   function auctionVault(AuctionVaultParams calldata params)
132:     external
133:     returns (OrderParameters memory);
=> missing return

File: ICollateralToken.sol
141:   function SEAPORT() external view returns (ConsiderationInterface);
=> missing NatSpec

File: IFlashAction.sol
23:   function onFlashAction(Underlying calldata, bytes calldata)
24:     external
25:     returns (bytes32);
=> missing NatSpec

File: ILienToken.sol
121:   function stopLiens(
122:     uint256 collateralId,
123:     uint256 auctionWindow,
124:     Stack[] calldata stack,
125:     address liquidator
126:   ) external;
=> missing params

File: ILienToken.sol
132:   function getBuyout(Stack calldata stack)
133:     external
134:     view
135:     returns (uint256 owed, uint256 buyout);
=> missing returns

File: ILienToken.sol
159:   function getInterest(Stack calldata stack) external returns (uint256);
=> missing return

File: ILienToken.sol
165:   function getCollateralState(uint256 collateralId)
166:     external
167:     view
168:     returns (bytes32);
=> missing return

File: ILienToken.sol
174:   function getAmountOwingAtLiquidation(ILienToken.Stack calldata stack)
175:     external
176:     view
177:     returns (uint256);
=> missing return

File: ILienToken.sol
183:   function createLien(LienActionEncumber memory params)
184:     external
185:     returns (
186:       uint256 lienId,
187:       Stack[] memory stack,
188:       uint256 slope
189:     );
=> missing return

File: ILienToken.sol
195:   function buyoutLien(LienActionBuyout memory params)
196:     external
197:     returns (Stack[] memory, Stack memory);
=> missing returns

File: ILienToken.sol
204:   function payDebtViaClearingHouse(
205:     address token,
206:     uint256 collateralId,
207:     uint256 payment,
208:     AuctionStack[] memory auctionStack
209:   ) external;
=> missing token

File: ILienToken.sol
216:   function makePayment(
217:     uint256 collateralId,
218:     Stack[] memory stack,
219:     uint256 amount
220:   ) external returns (Stack[] memory newStack);
=> missing collateralId and return

File: ILienToken.sol
222:   function makePayment(
223:     uint256 collateralId,
224:     Stack[] calldata stack,
225:     uint8 position,
226:     uint256 amount
227:   ) external returns (Stack[] memory newStack);
=> missing NatSpec

File: ILienToken.sol
248:   function getAuctionData(uint256 collateralId)
249:     external
250:     view
251:     returns (AuctionData memory);
=> missing return

File: ILienToken.sol
257:   function getAuctionLiquidator(uint256 collateralId)
258:     external
259:     view
260:     returns (address liquidator);
=> missing return

File: ILienToken.sol
266:   function getMaxPotentialDebtForCollateral(ILienToken.Stack[] memory stack)
267:     external
268:     view
269:     returns (uint256);
=> missing return

File: ILienToken.sol
276:   function getMaxPotentialDebtForCollateral(
277:     ILienToken.Stack[] memory stack,
278:     uint256 end
279:   ) external view returns (uint256);
=> missing return

File: IPublicVault.sol
94:   function getLienEpoch(uint64 end) external view returns (uint64);
=> missing return

File: IPublicVault.sol
110:   function timeToEpochEnd() external view returns (uint256);
111: 
112:   function timeToSecondEpochEnd() external view returns (uint256);
=> missing notice

File: IPublicVault.sol
140:   function handleBuyoutLien(BuyoutLienParams calldata params) external;
=> wrong NatSpec, missing @notice for desc

File: IPublicVault.sol
148:   function updateVaultAfterLiquidation(
149:     uint256 maxAuctionWindow,
150:     AfterLiquidationParams calldata params
151:   ) external returns (address withdrawProxyIfNearBoundary);
=> wrong NatSpec, missing @notice for desc

File: IRouterBase.sol
19:   function ROUTER() external view returns (IAstariaRouter);
20: 
21:   function IMPL_TYPE() external view returns (uint8);
=> missing NatSpec

File: ISecurityHook.sol
17:   function getState(address, uint256) external view returns (bytes32);
=> missing NatSpec

File: IStrategyValidator.sol
20:   function validateAndParse(
21:     IAstariaRouter.NewLienRequest calldata params,
22:     address borrower,
23:     address collateralTokenContract,
24:     uint256 collateralTokenId
25:   ) external view returns (bytes32, ILienToken.Details memory);
=> missing NatSpec

File: ITransferProxy.sol
17:   function tokenTransferFrom(
18:     address token,
19:     address from,
20:     address to,
21:     uint256 amount
22:   ) external;
=> missing NatSpec

File: IVaultImplementation.sol
070:   function commitToLien(
071:     IAstariaRouter.Commitment calldata params,
072:     address receiver
073:   )
074:     external
075:     returns (
076:       uint256 lienId,
077:       ILienToken.Stack[] memory stack,
078:       uint256 payout
079:     );
080: 
081:   function buyoutLien(
082:     ILienToken.Stack[] calldata stack,
083:     uint8 position,
084:     IAstariaRouter.Commitment calldata incomingTerms
085:   ) external returns (ILienToken.Stack[] memory, ILienToken.Stack memory);
086: 
087:   function recipient() external view returns (address);
088: 
089:   function setDelegate(address delegate_) external;
090: 
091:   function init(InitParams calldata params) external;
092: 
093:   function encodeStrategyData(
094:     IAstariaRouter.StrategyDetailsParam calldata strategy,
095:     bytes32 root
096:   ) external view returns (bytes memory);
097: 
098:   function domainSeparator() external view returns (bytes32);
099: 
100:   function modifyDepositCap(uint256 newCap) external;
101: 
102:   function getStrategistNonce() external view returns (uint256);
103: 
104:   function STRATEGY_TYPEHASH() external view returns (bytes32);
=> missing NatSpec for all functions

File: IWithdrawProxy.sol
47:   function drain(uint256 amount, address withdrawProxy)
48:     external
49:     returns (uint256);
=> missing return

File: IWithdrawProxy.sol
76:   function getFinalAuctionEnd() external view returns (uint256);
=> wrong NatSpec, missing @notice for description

```
