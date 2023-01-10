## Summary
|ID     | Finding|  Gas saved| Instances |
|:----: | :---           |              :----:    |  :----:         |
|1       | Storage variables read more than once in a function must be assigned to a memory variable | 100 | 1 |
| 2      |Redundant event | 18| 1 |
| 3      |Uint8 is more expensive than uint256 when it takes up a whole storage slot | 18| 1 |
| 4      |Combine 2 structs when they are almost identical | 18| 1 |
| 5      |Pass struct with position as parameter instead of array of struct and position | 18| 1 |
| 6      |If statement is unnecessary | 18| 1 |
| 3      |Miscellaneous| 0| 1 |

## Details
### 1 Storage variables read more than once in a function must be assigned to a memory variable
[CollateralToken.sol#L115-L130](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L115-L130)
Gas saved: 100
```diff 
	address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
+	bytes32 auction = s.collateralIdToAuction[collateralId];	
        if (
-         s.collateralIdToAuction[collateralId] == bytes32(0) ||
+        auction == bytes32(0) ||
          liquidator == address(0)
        ) {
         //revert no auction
         revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
        }
        if (
-         s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
+        auction != keccak256(abi.encode(params))
        ) {
         //revert auction params dont match
         revert InvalidCollateralState(
         InvalidCollateralStates.INVALID_AUCTION_PARAMS
       );
     }
```
[CollateralToken.sol#L299-L328](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L299-L328)
Gas saved: 277
```diff 
+   address clearingHouse = s.clearingHouse[collateralId];	
-    ClearingHouse(s.clearingHouse[collateralId]).transferUnderlying(
+   ClearingHouse(clearingHouse).transferUnderlying(
      addr,
      tokenId,
      address(receiver)
    );

    //trigger the flash action on the receiver
    if (
      receiver.onFlashAction(
-       IFlashAction.Underlying(s.clearingHouse[collateralId], addr, tokenId),
+       IFlashAction.Underlying(clearingHouse, addr, tokenId),
        data
      ) != keccak256("FlashAction.onFlashAction")
    ) {
      revert FlashActionCallbackFailed();
    }

    if (
      securityHook != address(0) &&
      preTransferState != ISecurityHook(securityHook).getState(addr, tokenId)
    ) {
      revert FlashActionSecurityCheckFailed();
    }

    // validate that the NFT returned after the call
    
    if (
-     IERC721(addr).ownerOf(tokenId) != address(s.clearingHouse[collateralId])
+     IERC721(addr).ownerOf(tokenId) != address(clearingHouse)
    ) {
      revert FlashActionNFTNotReturned();
    }
```
[CollateralToken.sol#L434-L474](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L434-L474)
Gas saved: 565
```diff 
L434
    Asset memory underlying = s.idToUnderlying[collateralId];
+   address clearingHouse = s.clearingHouse[collateralId];

L451
    considerationItems[0] = ConsiderationItem(
      ItemType.ERC20,
      settlementToken,
      uint256(0),
      prices[0],
      prices[1],
-     payable(address(s.clearingHouse[collateralId]))
+     payable(address(clearingHouse))
    );
    considerationItems[1] = ConsiderationItem(
      ItemType.ERC1155,
-     s.clearingHouse[collateralId],
+     clearingHouse
      uint256(uint160(settlementToken)),
      prices[0],
      prices[1],
-     payable(s.clearingHouse[collateralId])
+     payable(address(clearingHouse))
    );

    orderParameters = OrderParameters({
-     offerer: s.clearingHouse[collateralId],
+     offerer: clearingHouse,
      ""
    });
```
[WithdrawProxy.sol#L241-266](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L241-266)
Gas saved: 135
```diff 
L241
    WPStorage storage s = _loadSlot();
+  uint88 expected = s.expected;
    if (s.finalAuctionEnd == 0) {
      revert InvalidState(InvalidStates.CANT_CLAIM);
    }
L 258
-    if (balance < s.expected) {
+    if (balance < expected) {
      PublicVault(VAULT()).decreaseYIntercept(
-       (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
+       (expected - balance).mulWadDown(1e18 - s.withdrawRatio)
      );
    } else {
      PublicVault(VAULT()).increaseYIntercept(
-       (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
+       (balance - expected).mulWadDown(1e18 - s.withdrawRatio)
      );
    }
```
## 2 Redundant event
Combining the event AddLien and LienStackUpdated can be a huge gas saver
Average gas saved: 1354
[ILienToken.sol#L294-L311](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ILienToken.sol#L294-L311)
```diff
L294
-  event AddLien(
-    uint256 indexed collateralId,
-    uint8 position,
-    uint256 indexed lienId,
-    Stack stack
-  );

L306
  event LienStackUpdated(
    uint256 indexed collateralId,
    uint8 position,
    StackAction action,
    uint8 stackLength,
+   uint256 indexed lienId,
+   Stack stack,
  );
```
[LienToken.sol#L409-L421](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L409-L421)
```diff
L410
-    emit AddLien(
-      params.lien.collateralId,
-      uint8(params.stack.length),
-      lienId,
-      newStackSlot
-    );
    emit LienStackUpdated(
      params.lien.collateralId,
      uint8(params.stack.length),
      StackAction.ADD,
      uint8(newStack.length)
+     lienId,
+     newStackSlot
    );
```
However for this to work the emmitting of LienStackUpdated in [LienToken.sol#L875-L879](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L875-L879) also needs to be changed. This might lower readability but it's a big gas saver. 
In addition to this, the [RemovedLiens](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ILienToken.sol#L308) event is never used so it can be removed. 

## 3 Uint smaller then uint256 is more expensive than uint256 when it takes up a whole storage slot
If your data is smaller, further operations are needed to downscale from 256 bits to 8 bits
[ILienToken.sol#L60-L67](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L60-L67)
```diff
  struct Lien {
-   uint8 collateralType;
-   uint256 collateralType 
    address token; //20
    address vault; //20
    bytes32 strategyRoot; //32
    uint256 collateralId; //32 //contractAddress + tokenId
    Details details; //32 * 5
  }
```
|Function name | uint8: avg gas|   uint256: avg gas| gas saved|
|:----: | :---           |              :----:    |  :----:         |  
|buyoutLien| 54205 | 53882| 323 |
| createLien|84971 | 84805|166 |
| getAuctionLiquidator|31078 | 31078 |0 |
| getBuyout|736| 758|-22 |
| getBuyout|11024|10830| 194|
| getCollateralState|619| 662|-43 |
| getOwed|3570| 3433|137 |
| getOwed|3571| 3480| 91|
| getPayee|1972| 1883|89 |
| initialize|161943| 161987| -44|
| makePayment|42967| 42790| 177|
|payDebtViaClearingHouse|51430| 51358| 72|
|stopLiens|297791| 297687|104 |

Total gas saved: 1244
Similar result can be made for struct [StrategyDetailsParam](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L101-L05) and [NewLienRequest](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#112-L121)
For the struct point there can be also be gas optimizations if youmake  the 3 variables counting up to uint256
[ILienToken.sol#L69-L74](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L69-L74)

## 4 Combine 2 structs when they are almost identical
```diff
  struct LienActionEncumber {
    uint256 amount;
    address receiver;
    ILienToken.Lien lien;
    Stack[] stack;
+   uint8 position; 
  }

-  struct LienActionBuyout {
-    uint8 position;
-    LienActionEncumber encumber;
-  }
```
function buyoutLien: avg gas saved: 1064
## 5 Pass struct with position as paremeter instead of array of struct and position
Average gas saved: 252
[LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L512-L523)
```diff
  function _payDebt(
    LienStorage storage s,
    address token,
    uint256 payment,
    address payer,
    AuctionStack[] memory stack
  ) internal returns (uint256 totalSpent) {
    uint256 i;
    for (; i < stack.length;) {
      uint256 spent;
      unchecked {
-       spent = _paymentAH(s, token, stack, i, payment, payer);
+       spent = _paymentAH(s, token, stack[i], payment, payer);
        totalSpent += spent;
        payment -= spent;
        ++i;
      }
    }
  }
  
_paymentAH()  
L623
  function _paymentAH(
    LienStorage storage s,
    address token,
-   AuctionStack[] memory stack,
+   AuctionStack memory stack
-   uint256 position,
    uint256 payment,
    address payer
  ) internal returns (uint256) {
-   uint256 lienId = stack[position].lienId;
+   uint256 lienId = stack.lienId;
-   uint256 end = stack[position].end;
+   uint256 end = stack.end;
-   uint256 owing = stack[position].amountOwed;
+   uint256 owing = stack.amountOwed;
    ""
L645
-   delete stack[position];
+   delete stack;
    ""
  }
```
## 6 if statement is unnecessary 
[PublicVault.sol#L103-L107](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L103-L107)
Deposit function avg gas saved: 735
Decimals will always be 18 in this case.
```solidity
    if (ERC20(asset()).decimals() == uint8(18)) {
      return 100 gwei;
    } else {
      return 10**(ERC20(asset()).decimals() - 1);
    }
```
## Miscellaneous
### Unused variable
deposit() function avg gas saved: 1149
[PublicVault.sol#L262](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L262)
assets is never used and never returned.
```solidity
L262:   uint256 assets = totalAssets();
```

### currentWithdrawProxy is already defined so there is no need to do it twice
transferWithdrawReserve() avg gas saved: 81
On line [L366-L368](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L366-L368) currentWithdrawProxy is defined. However on [L397-L399](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L397-L399) it's defined again exactly in the same way. This can be removed.

On [L402](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L402) it's accessed the same as the variable so it's better to use the memory variable instead of reading from storage again.
```diff
L366
      address currentWithdrawProxy = s
       .epochData[s.currentEpoch - 1]
       .withdrawProxy;
     
L397
-      address currentWithdrawProxy = s
-        .epochData[s.currentEpoch - 1]
-        .withdrawProxy;
      uint256 drainBalance = WithdrawProxy(withdrawProxy).drain(
        s.withdrawReserve,
-       s.epochData[s.currentEpoch - 1].withdrawProxy
+       currentWithdrawProxy 
      );
```
