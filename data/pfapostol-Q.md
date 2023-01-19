# Summary

In general, the codebase is well written, but it could be improved by adding more documentation and moving some of the documentation from interfaces to actual implementations.

## QA Issues

|       | Issue                                                  | Instances |
| :---: | :----------------------------------------------------- | :-------: |
| **1** | Check approval before calling `safeTransferFrom`       |     1     |
| **2** | Add documentation to `struct`s                         |    33     |
| **3** | `validateOrder` should revert or return bool           |     1     |
| **4** | Code is duplicated instead of using existing functions |     1     |
| **5** | `ERC4626` compatible `Vault`s                          |     1     |

## Total: 37 instances over 5 issues

---

#### 01. **Check approval before calling `safeTransferFrom` (1 instances)**

- src/AstariaRouter.sol

`pullToken` is a `public` function that calls `safeTransferFrom` via `TransferProxy`. `TransferProxy` must be approved for transfer, otherwise the user will get meaningless errors such as: `Arithmetic overflow/underflow`, `TRANSFER_FROM_FAILED`. `AstariaRouter` can check that `permission` is below amount and return with an explicit error, such as "You must first approve `TransferProxy`"

##### - [src/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)

```diff
diff --git a/src/AstariaRouter.sol b/src/AstariaRouter.sol
index c797baa..e032764 100644
--- a/src/AstariaRouter.sol
+++ b/src/AstariaRouter.sol
@@ -206,6 +206,9 @@ contract AstariaRouter is
  206, 206:     address recipient
  207, 207:   ) public payable override {
  208, 208:     RouterStorage storage s = _loadRouterSlot();
+      209:+    if (ERC20(token).allowance(msg.sender, address(s.TRANSFER_PROXY)) < amount) {
+      210:+      revert("You must approve `TransferProxy` first");
+      211:+    }
  209, 212:     s.TRANSFER_PROXY.tokenTransferFrom(
  210, 213:       address(token),
  211, 214:       msg.sender,
```

---

#### 02. **Add documentation to `struct`s (33 instances)**

Each field of the structure should be documented to make it easier for the user to understand the code.

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IAstariaRouter.sol#L49-L52

```solidity
struct File {
    FileType what;
    bytes data;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IAstariaRouter.sol#L56-L85

```solidity
struct RouterStorage {
    //slot 1
    uint32 auctionWindow;
    uint32 auctionWindowBuffer;
    uint32 liquidationFeeNumerator;
    uint32 liquidationFeeDenominator;
    uint32 maxEpochLength;
    uint32 minEpochLength;
    uint32 protocolFeeNumerator;
    uint32 protocolFeeDenominator;
    //slot 2
    ERC20 WETH; //20
    ICollateralToken COLLATERAL_TOKEN; //20
    ILienToken LIEN_TOKEN; //20
    ITransferProxy TRANSFER_PROXY; //20
    address feeTo; //20
    address BEACON_PROXY_IMPLEMENTATION; //20
    uint88 maxInterestRate; //6
    uint32 minInterestBPS; // was uint64
    //slot 3 +
    address guardian; //20
    address newGuardian; //20
    uint32 buyoutFeeNumerator;
    uint32 buyoutFeeDenominator;
    uint32 minDurationIncrease;
    mapping(uint8 => address) strategyValidators;
    mapping(uint8 => address) implementations;
    //A strategist can have many deployed vaults
    mapping(address => bool) vaults;
  }
```


https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IAstariaRouter.sol#L101-L127

```solidity
struct StrategyDetailsParam {
    uint8 version;
    uint256 deadline;
    address vault;
  }

  struct MerkleData {
    bytes32 root;
    bytes32[] proof;
  }

  struct NewLienRequest {
    StrategyDetailsParam strategy;
    ILienToken.Stack[] stack;
    bytes nlrDetails;
    MerkleData merkle;
    uint256 amount;
    uint8 v;
    bytes32 r;
    bytes32 s;
  }

  struct Commitment {
    address tokenContract;
    uint256 tokenId;
    NewLienRequest lienRequest;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ICollateralToken.sol#L46-L72

```solidity
struct Asset {
    address tokenContract;
    uint256 tokenId;
  }

  struct CollateralStorage {
    ITransferProxy TRANSFER_PROXY;
    ILienToken LIEN_TOKEN;
    IAstariaRouter ASTARIA_ROUTER;
    ConsiderationInterface SEAPORT;
    ConduitControllerInterface CONDUIT_CONTROLLER;
    address CONDUIT;
    bytes32 CONDUIT_KEY;
    mapping(uint256 => bytes32) collateralIdToAuction;
    mapping(address => bool) flashEnabled;
    //mapping of the collateralToken ID and its underlying asset
    mapping(uint256 => Asset) idToUnderlying;
    //mapping of a security token hook for an nft's token contract address
    mapping(address => address) securityHooks;
    mapping(uint256 => address) clearingHouse;
  }

  struct ListUnderlyingForSaleParams {
    ILienToken.Stack[] stack;
    uint256 listPrice;
    uint56 maxDuration;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ICollateralToken.sol#L82-L85

```solidity
struct File {
    FileType what;
    bytes data;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ICollateralToken.sol#L119-L125

```solidity
struct AuctionVaultParams {
    address settlementToken;
    uint256 collateralId;
    uint256 maxDuration;
    uint256 startingPrice;
    uint256 endingPrice;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IFlashAction.sol#L17-L21

```solidity
struct Underlying {
    address returnTarget;
    address token;
    uint256 tokenId;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ILienToken.sol#L29-L32

```solidity
struct File {
    FileType what;
    bytes data;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ILienToken.sol#L36-L91

```solidity
struct LienStorage {
    uint8 maxLiens;
    address WETH;
    ITransferProxy TRANSFER_PROXY;
    IAstariaRouter ASTARIA_ROUTER;
    ICollateralToken COLLATERAL_TOKEN;
    mapping(uint256 => bytes32) collateralStateHash;
    mapping(uint256 => AuctionData) auctionData;
    mapping(uint256 => LienMeta) lienMeta;
  }

  struct LienMeta {
    address payee;
    bool atLiquidation;
  }

  struct Details {
    uint256 maxAmount;
    uint256 rate; //rate per second
    uint256 duration;
    uint256 maxPotentialDebt;
    uint256 liquidationInitialAsk;
  }

  struct Lien {
    uint8 collateralType;
    address token; //20
    address vault; //20
    bytes32 strategyRoot; //32
    uint256 collateralId; //32 //contractAddress + tokenId
    Details details; //32 * 5
  }

  struct Point {
    uint88 amount; //11
    uint40 last; //5
    uint40 end; //5
    uint256 lienId; //32
  }

  struct Stack {
    Lien lien;
    Point point;
  }

  struct LienActionEncumber {
    uint256 amount;
    address receiver;
    ILienToken.Lien lien;
    Stack[] stack;
  }

  struct LienActionBuyout {
    uint8 position;
    LienActionEncumber encumber;
  }
```

https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/ILienToken.sol#L229-L242

```solidity
struct AuctionStack {
    uint256 lienId;
    uint88 amountOwed;
    uint40 end;
  }

  struct AuctionData {
    uint88 startAmount;
    uint88 endAmount;
    uint48 startTime;
    uint48 endTime;
    address liquidator;
    AuctionStack[] stack;
  }
```


https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IPublicVault.sol#L20-L56

```solidity
struct EpochData {
    uint64 liensOpenForEpoch;
    address withdrawProxy;
  }

  struct VaultData {
    uint88 yIntercept;
    uint48 slope;
    uint40 last;
    uint64 currentEpoch;
    uint88 withdrawReserve;
    uint88 liquidationWithdrawRatio;
    uint88 strategistUnclaimedShares;
    mapping(uint64 => EpochData) epochData;
  }

  struct BeforePaymentParams {
    uint256 lienSlope;
    uint256 amount;
    uint256 interestOwed;
  }

  struct BuyoutLienParams {
    uint256 lienSlope;
    uint256 lienEnd;
    uint256 increaseYIntercept;
  }

  struct AfterLiquidationParams {
    uint256 lienSlope;
    uint256 newAmount;
    uint40 lienEnd;
  }

  struct LiquidationPaymentParams {
    uint256 remaining;
  }
```


https://github.com/code-423n4/2023-01-astaria/blob/91bf1f55b3df706da81026c4cf755ced330fd5ed/src/interfaces/IV3PositionManager.sol#L73-L85

```solidity
struct MintParams {
    address token0;
    address token1;
    uint24 fee;
    int24 tickLower;
    int24 tickUpper;
    uint256 amount0Desired;
    uint256 amount1Desired;
    uint256 amount0Min;
    uint256 amount1Min;
    address recipient;
    uint256 deadline;
  }
```


.......

---

#### 03. **`validateOrder` should revert or return bool (1 instances)**

`validateOrder` do not return or check anything

##### - [src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

```diff
diff --git a/src/ClearingHouse.sol b/src/ClearingHouse.sol
index 5c2a400..be431ce 100644
--- a/src/ClearingHouse.sol
+++ b/src/ClearingHouse.sol
@@ -204,7 +204,9 @@ contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {
  204, 204:       ASTARIA_ROUTER.COLLATERAL_TOKEN().getConduit(),
  205, 205:       order.parameters.offer[0].identifierOrCriteria
  206, 206:     );
- 207     :-    ASTARIA_ROUTER.COLLATERAL_TOKEN().SEAPORT().validate(listings);
+      207:+    if (!ASTARIA_ROUTER.COLLATERAL_TOKEN().SEAPORT().validate(listings)) {
+      208:+      revert("Invalid orders");
+      209:+    }
  208, 210:   }
  209, 211: 
  210, 212:   function transferUnderlying(
```

---

#### 04. **Code is duplicated instead of using existing functions (1 instance)**

##### - [src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

function 47: `COLLATERAL_ID` defined but not used in 136

```diff
diff --git a/src/ClearingHouse.sol b/src/ClearingHouse.sol
index 5c2a400..ae38f77 100644
--- a/src/ClearingHouse.sol
+++ b/src/ClearingHouse.sol
@@ -133,7 +133,7 @@ contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {
  133, 133: 
  134, 134:     require(payment >= currentOfferPrice, "not enough funds received");
  135, 135: 
- 136     :-    uint256 collateralId = _getArgUint256(21);
+      136:+    uint256 collateralId = COLLATERAL_ID();
  137, 137:     // pay liquidator fees here
  138, 138: 
  139, 139:     ILienToken.AuctionStack[] storage stack = s.auctionStack.stack;
```

---

#### 05. **`ERC4626` compatible `Vault`s (1 instance)**

Private `Vault` does not implement `ERC4626`, but the user can call `ERC4626` functions using `AstariaRouter` (`mint` and `redeem`). If the private `Vault` is not intended for the full `ERC4626` support, `AstariaRouter` should check the type of `Vault` and return with a user-friendly error. Or the functions in `AstariaRouter` should only accept `IPublicVault`.

##### - [src/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)

```diff
diff --git a/src/AstariaRouter.sol b/src/AstariaRouter.sol
index c797baa..26c1733 100644
--- a/src/AstariaRouter.sol
+++ b/src/AstariaRouter.sol
@@ -133,6 +133,9 @@ contract AstariaRouter is
  133, 133:     validVault(address(vault))
  134, 134:     returns (uint256 amountIn)
  135, 135:   {
+      136:+    if (IAstariaVaultBase(address(vault)).IMPL_TYPE() == 0) {
+      137:+      revert("Only public vaults");
+      138:+    }
  136, 139:     return super.mint(vault, to, shares, maxAmountIn);
  137, 140:   }
  138, 141: 
@@ -181,6 +184,9 @@ contract AstariaRouter is
  181, 184:     validVault(address(vault))
  182, 185:     returns (uint256 amountOut)
  183, 186:   {
+      187:+    if (IAstariaVaultBase(address(vault)).IMPL_TYPE() == 0) {
+      188:+      revert("Only public vaults");
+      189:+    }
  184, 190:     return super.redeem(vault, to, shares, minAmountOut);
  185, 191:   }
  186, 192: 
```

---