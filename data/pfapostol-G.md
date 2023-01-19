##### Gas Optimizations

Gas savings are estimated using the gas report of existing `forge test --ffi --fork-url "https://eth-mainnet.g.alchemy.com/v2/<API_KEY>" --fork-block-number 15934974 --gas-report --fuzz-seed 1337` tests (the sum of all deployment costs and the sum of the costs of calling methods) and may vary depending on the implementation of the fix.

**NOTE**: foundry.toml

```toml
gas_reports_ignore = ["Strings2", "MockERC721", "MultiRolesAuthority", "TestNFT", "AstariaTest", "Conduit", "ForkedTesting", "IntegrationTest", "RevertTesting", "WithdrawTest", "AstariaTest", "ConduitController", "Consideration", "TransparentUpgradeableProxy", "WETH"]
```


Sorted by average user savings.
|       | Issue                                                                               | Instances | Estimated gas(deployments) | Estimated gas(min method call) | Estimated gas(avg method call) | Estimated gas(max method call) |
| :---: | :---------------------------------------------------------------------------------- | :-------: | :------------------------: | :----------------------------: | :----------------------------: | :----------------------------: |
| **1** | Multiple accesses of a mapping/array should use a local variable cache              |    18     |           54 480           |             1 919              |             2 698              |             4 359              |
| **2** | Use of the memory keyword when storage pointer should be used                       |     3     |           17 016           |              336               |              408               |              403               |
| **3** | Duplicated require()/revert() checks should be refactored to a modifier or function |     4     |           75 296           |              -163              |              -172              |              -163              |
| **4** | Instead of using a modifier you can save gas using private view functions           |     9     |          460 617           |             -1 250             |             -1 627             |             -2 415             |
|       | **Overall gas savings**                                                             |  **34**   |     **622 011 (2,3%)**     |          **689 (0%)**          |         **1 177 (0%)**         |         **2 057 (0%)**         |



**Total: 34 instances over 4 issues**

---

#### 01. **Multiple accesses of a mapping/array should use a local variable cache (18 instances)**

Deployment. Gas Saved: **54 480**

Minimum Method Call. Gas Saved: **1 919**

Average Method Call. Gas Saved: **2 698**

Maximum Method Call. Gas Saved: **4 359**

Overall gas change: **-18 316 (-0.429%)**

##### - src/CollateralToken.sol:[117](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L117), [124](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L124), [299](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L299), [308](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L308), [325](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L325), [451](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L451), [455](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L455), [459](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L459), [463](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L463)

```diff
diff --git a/src/CollateralToken.sol b/src/CollateralToken.sol
index c82b400..00ef01e 100644
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol
@@ -113,15 +113,16 @@ contract CollateralToken is
  113, 113:       params.offer[0].identifierOrCriteria
  114, 114:     );
  115, 115:     address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
+      116:+    bytes32 auction = s.collateralIdToAuction[collateralId];
  116, 117:     if (
- 117     :-      s.collateralIdToAuction[collateralId] == bytes32(0) ||
+      118:+      auction == bytes32(0) ||
  118, 119:       liquidator == address(0)
  119, 120:     ) {
  120, 121:       //revert no auction
  121, 122:       revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
  122, 123:     }
  123, 124:     if (
- 124     :-      s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
+      125:+      auction != keccak256(abi.encode(params))
  125, 126:     ) {
  126, 127:       //revert auction params dont match
  127, 128:       revert InvalidCollateralState(
@@ -295,8 +296,9 @@ contract CollateralToken is
  295, 296:       preTransferState = ISecurityHook(securityHook).getState(addr, tokenId);
  296, 297:     }
  297, 298:     // transfer the NFT to the destination optimistically
+      299:+    address _clearingHouse = s.clearingHouse[collateralId];
  298, 300: 
- 299     :-    ClearingHouse(s.clearingHouse[collateralId]).transferUnderlying(
+      301:+    ClearingHouse(_clearingHouse).transferUnderlying(
  300, 302:       addr,
  301, 303:       tokenId,
  302, 304:       address(receiver)
@@ -305,7 +307,7 @@ contract CollateralToken is
  305, 307:     //trigger the flash action on the receiver
  306, 308:     if (
  307, 309:       receiver.onFlashAction(
- 308     :-        IFlashAction.Underlying(s.clearingHouse[collateralId], addr, tokenId),
+      310:+        IFlashAction.Underlying(_clearingHouse, addr, tokenId),
  309, 311:         data
  310, 312:       ) != keccak256("FlashAction.onFlashAction")
  311, 313:     ) {
@@ -322,7 +324,7 @@ contract CollateralToken is
  322, 324:     // validate that the NFT returned after the call
  323, 325: 
  324, 326:     if (
- 325     :-      IERC721(addr).ownerOf(tokenId) != address(s.clearingHouse[collateralId])
+      327:+      IERC721(addr).ownerOf(tokenId) != _clearingHouse
  326, 328:     ) {
  327, 329:       revert FlashActionNFTNotReturned();
  328, 330:     }
@@ -441,6 +443,8 @@ contract CollateralToken is
  441, 443:       1
  442, 444:     );
  443, 445: 
+      446:+    address _clearingHouse = s.clearingHouse[collateralId];
+      447:+
  444, 448:     ConsiderationItem[] memory considerationItems = new ConsiderationItem[](2);
  445, 449:     considerationItems[0] = ConsiderationItem(
  446, 450:       ItemType.ERC20,
@@ -448,19 +452,19 @@ contract CollateralToken is
  448, 452:       uint256(0),
  449, 453:       prices[0],
  450, 454:       prices[1],
- 451     :-      payable(address(s.clearingHouse[collateralId]))
+      455:+      payable(_clearingHouse)
  452, 456:     );
  453, 457:     considerationItems[1] = ConsiderationItem(
  454, 458:       ItemType.ERC1155,
- 455     :-      s.clearingHouse[collateralId],
+      459:+      _clearingHouse,
  456, 460:       uint256(uint160(settlementToken)),
  457, 461:       prices[0],
  458, 462:       prices[1],
- 459     :-      payable(s.clearingHouse[collateralId])
+      463:+      payable(_clearingHouse)
  460, 464:     );
  461, 465: 
  462, 466:     orderParameters = OrderParameters({
- 463     :-      offerer: s.clearingHouse[collateralId],
+      467:+      offerer: _clearingHouse,
  464, 468:       zone: address(this), // 0x20
  465, 469:       offer: offer,
  466, 470:       consideration: considerationItems,
```

##### - src/PublicVault.sol:[287](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L287), [291](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L291), [293](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L293), [303](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L303), [362](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L362), [366](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366), [391](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L391), [397](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L397), [402](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L402)

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..a7fb447 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -283,14 +283,17 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  283, 283:       revert InvalidState(InvalidStates.WITHDRAW_RESERVE_NOT_ZERO);
  284, 284:     }
  285, 285: 
+      286:+    uint64 _currentEpoch = s.currentEpoch;
+      287:+    EpochData storage _currentEpochData = s.epochData[_currentEpoch];
+      288:+
  286, 289:     WithdrawProxy currentWithdrawProxy = WithdrawProxy(
- 287     :-      s.epochData[s.currentEpoch].withdrawProxy
+      290:+      _currentEpochData.withdrawProxy
  288, 291:     );
  289, 292: 
  290, 293:     // split funds from previous WithdrawProxy with PublicVault if hasn't been already
- 291     :-    if (s.currentEpoch != 0) {
+      294:+    if (_currentEpoch != 0) {
  292, 295:       WithdrawProxy previousWithdrawProxy = WithdrawProxy(
- 293     :-        s.epochData[s.currentEpoch - 1].withdrawProxy
+      296:+        s.epochData[_currentEpoch - 1].withdrawProxy
  294, 297:       );
  295, 298:       if (
  296, 299:         address(previousWithdrawProxy) != address(0) &&
@@ -300,7 +303,7 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  300, 303:       }
  301, 304:     }
  302, 305: 
- 303     :-    if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0) {
+      306:+    if (_currentEpochData.liensOpenForEpoch > 0) {
  304, 307:       revert InvalidState(InvalidStates.LIENS_OPEN_FOR_EPOCH_NOT_ZERO);
  305, 308:     }
  306, 309: 
@@ -359,13 +362,14 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  359, 362:   function transferWithdrawReserve() public {
  360, 363:     VaultData storage s = _loadStorageSlot();
  361, 364: 
- 362     :-    if (s.currentEpoch == uint64(0)) {
+      365:+    uint64 _currentEpoch = s.currentEpoch;
+      366:+    address _lastEpochWithdrawProxy = s.epochData[_currentEpoch - 1].withdrawProxy;
+      367:+
+      368:+    if (_currentEpoch == uint64(0)) {
  363, 369:       return;
  364, 370:     }
  365, 371: 
- 366     :-    address currentWithdrawProxy = s
- 367     :-      .epochData[s.currentEpoch - 1]
- 368     :-      .withdrawProxy;
+      372:+    address currentWithdrawProxy = _lastEpochWithdrawProxy;
  369, 373:     // prevents transfer to a non-existent WithdrawProxy
  370, 374:     // withdrawProxies are indexed by the epoch where they're deployed
  371, 375:     if (currentWithdrawProxy != address(0)) {
@@ -388,18 +392,16 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  388, 392:       emit WithdrawReserveTransferred(withdrawBalance);
  389, 393:     }
  390, 394: 
- 391     :-    address withdrawProxy = s.epochData[s.currentEpoch].withdrawProxy;
+      395:+    address withdrawProxy = s.epochData[_currentEpoch].withdrawProxy;
  392, 396:     if (
  393, 397:       s.withdrawReserve > 0 &&
  394, 398:       timeToEpochEnd() == 0 &&
  395, 399:       withdrawProxy != address(0)
  396, 400:     ) {
- 397     :-      address currentWithdrawProxy = s
- 398     :-        .epochData[s.currentEpoch - 1]
- 399     :-        .withdrawProxy;
+      401:+      address currentWithdrawProxy = _lastEpochWithdrawProxy;
  400, 402:       uint256 drainBalance = WithdrawProxy(withdrawProxy).drain(
  401, 403:         s.withdrawReserve,
- 402     :-        s.epochData[s.currentEpoch - 1].withdrawProxy
+      404:+        _lastEpochWithdrawProxy
  403, 405:       );
  404, 406:       unchecked {
  405, 407:         s.withdrawReserve -= drainBalance.safeCastTo88();
```

---

#### 02. **Use of the memory keyword when storage pointer should be used (3 instances)**

Deployment. Gas Saved: **17 016**

Minimum Method Call. Gas Saved: **336**

Average Method Call. Gas Saved: **408**

Maximum Method Call. Gas Saved: **403**

Overall gas change: **-5 072 (-0.151%)**

##### - src/CollateralToken.sol:[390](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L390), [434](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L434), [562](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L562)

```diff
diff --git a/src/CollateralToken.sol b/src/CollateralToken.sol
index c82b400..10d2eb8 100644
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol
@@ -387,7 +387,7 @@ contract CollateralToken is
  387, 387:     view
  388, 388:     returns (address, uint256)
  389, 389:   {
- 390     :-    Asset memory underlying = _loadCollateralSlot().idToUnderlying[
+      390:+    Asset storage underlying = _loadCollateralSlot().idToUnderlying[
  391, 391:       collateralId
  392, 392:     ];
  393, 393:     return (underlying.tokenContract, underlying.tokenId);
@@ -431,7 +431,7 @@ contract CollateralToken is
  431, 431:   ) internal returns (OrderParameters memory orderParameters) {
  432, 432:     OfferItem[] memory offer = new OfferItem[](1);
  433, 433: 
- 434     :-    Asset memory underlying = s.idToUnderlying[collateralId];
+      434:+    Asset storage underlying = s.idToUnderlying[collateralId];
  435, 435: 
  436, 436:     offer[0] = OfferItem(
  437, 437:       ItemType.ERC721,
@@ -559,7 +559,7 @@ contract CollateralToken is
  559, 559:     CollateralStorage storage s = _loadCollateralSlot();
  560, 560:     uint256 collateralId = msg.sender.computeId(tokenId_);
  561, 561: 
- 562     :-    Asset memory incomingAsset = s.idToUnderlying[collateralId];
+      562:+    Asset storage incomingAsset = s.idToUnderlying[collateralId];
  563, 563:     if (incomingAsset.tokenContract == address(0)) {
  564, 564:       require(ERC721(msg.sender).ownerOf(tokenId_) == address(this));
  565, 565: 
```

---

#### 03. **Duplicated require()/revert() checks should be refactored to a modifier or function (4 instances)**

Deployment. Gas Saved: **75 296**

Minimum Method Call. Gas Saved: **-163**

Average Method Call. Gas Saved: **-172**

Maximum Method Call. Gas Saved: **-162**

Overall gas change: **238 (0.018%)**

##### - src/AstariaRouter.sol:[341](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L341)

```diff
diff --git a/src/AstariaRouter.sol b/src/AstariaRouter.sol
index c797baa..1582822 100644
--- a/src/AstariaRouter.sol
+++ b/src/AstariaRouter.sol
@@ -71,6 +71,11 @@ contract AstariaRouter is
   71,  71:     _disableInitializers();
   72,  72:   }
   73,  73: 
+       74:+  function is_guardian() private view {
+       75:+    RouterStorage storage s = _loadRouterSlot();
+       76:+    require(msg.sender == s.guardian);
+       77:+  }
+       78:+
   74,  79:   /**
   75,  80:    * @dev Setup transfer authority and set up addresses for deployed CollateralToken, LienToken, TransferProxy contracts, as well as PublicVault and SoloVault implementations to clone.
   76,  81:    * @param _AUTHORITY The authority manager.
@@ -337,14 +342,14 @@ contract AstariaRouter is
  337, 342:   }
  338, 343: 
  339, 344:   function setNewGuardian(address _guardian) external {
+      345:+    is_guardian();
  340, 346:     RouterStorage storage s = _loadRouterSlot();
- 341     :-    require(msg.sender == s.guardian);
  342, 347:     s.newGuardian = _guardian;
  343, 348:   }
  344, 349: 
  345, 350:   function __renounceGuardian() external {
+      351:+    is_guardian();
  346, 352:     RouterStorage storage s = _loadRouterSlot();
- 347     :-    require(msg.sender == s.guardian);
  348, 353:     s.guardian = address(0);
  349, 354:     s.newGuardian = address(0);
  350, 355:   }
@@ -357,8 +362,8 @@ contract AstariaRouter is
  357, 362:   }
  358, 363: 
  359, 364:   function fileGuardian(File[] calldata file) external {
+      365:+    is_guardian();
  360, 366:     RouterStorage storage s = _loadRouterSlot();
- 361     :-    require(msg.sender == address(s.guardian));
  362, 367: 
  363, 368:     uint256 i;
  364, 369:     for (; i < file.length; ) {
```

##### - src/ClearingHouse.sol:[199](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L199)

```diff
diff --git a/src/ClearingHouse.sol b/src/ClearingHouse.sol
index 5c2a400..379419a 100644
--- a/src/ClearingHouse.sol
+++ b/src/ClearingHouse.sol
@@ -40,6 +40,10 @@ contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {
   40,  40:   uint256 private constant CLEARING_HOUSE_STORAGE_SLOT =
   41,  41:     uint256(keccak256("xyz.astaria.ClearingHouse.storage.location")) - 1;
   42,  42: 
+       43:+  function is_collateral(address c) private view {
+       44:+    require(msg.sender == c);
+       45:+  }
+       46:+
   43,  47:   function ROUTER() public pure returns (IAstariaRouter) {
   44,  48:     return IAstariaRouter(_getArgAddress(0));
   45,  49:   }
@@ -196,7 +200,7 @@ contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {
  196, 200: 
  197, 201:   function validateOrder(Order memory order) external {
  198, 202:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
- 199     :-    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
+      203:+    is_collateral(address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  200, 204:     Order[] memory listings = new Order[](1);
  201, 205:     listings[0] = order;
  202, 206: 
@@ -213,14 +217,15 @@ contract ClearingHouse is AmountDeriver, Clone, IERC1155, IERC721Receiver {
  213, 217:     address target
  214, 218:   ) external {
  215, 219:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
- 216     :-    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
+      220:+
+      221:+    is_collateral(address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  217, 222:     ERC721(tokenContract).safeTransferFrom(address(this), target, tokenId);
  218, 223:   }
  219, 224: 
  220, 225:   function settleLiquidatorNFTClaim() external {
  221, 226:     IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
  222, 227: 
- 223     :-    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
+      228:+    is_collateral(address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
  224, 229:     ClearingHouseStorage storage s = _getStorage();
  225, 230:     ASTARIA_ROUTER.LIEN_TOKEN().payDebtViaClearingHouse(
  226, 231:       address(0),
```

##### - src/VaultImplementation.sol:[78](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L78)

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..e49b6a4 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -50,6 +50,10 @@ abstract contract VaultImplementation is
   50,  50:     );
   51,  51:   bytes32 constant VERSION = keccak256("0");
   52,  52: 
+       53:+  function is_owner() private view {
+       54:+    require(msg.sender == owner());
+       55:+  }
+       56:+
   53,  57:   function name() external view virtual override returns (string memory);
   54,  58: 
   55,  59:   function symbol() external view virtual override returns (string memory);
@@ -75,7 +79,7 @@ abstract contract VaultImplementation is
   75,  79:    * @param newCap The deposit cap.
   76,  80:    */
   77,  81:   function modifyDepositCap(uint256 newCap) external {
-  78     :-    require(msg.sender == owner()); //owner is "strategist"
+       82:+    is_owner(); //owner is "strategist"
   79,  83:     _loadVISlot().depositCap = newCap.safeCastTo88();
   80,  84:   }
   81,  85: 
@@ -93,7 +97,7 @@ abstract contract VaultImplementation is
   93,  97:    * @param enabled the status of the depositor
   94,  98:    */
   95,  99:   function modifyAllowList(address depositor, bool enabled) external virtual {
-  96     :-    require(msg.sender == owner()); //owner is "strategist"
+      100:+    is_owner(); //owner is "strategist"
   97, 101:     _loadVISlot().allowList[depositor] = enabled;
   98, 102:     emit AllowListUpdated(depositor, enabled);
   99, 103:   }
@@ -102,7 +106,7 @@ abstract contract VaultImplementation is
  102, 106:    * @notice disable the allowList for the vault
  103, 107:    */
  104, 108:   function disableAllowList() external virtual {
- 105     :-    require(msg.sender == owner()); //owner is "strategist"
+      109:+    is_owner(); //owner is "strategist"
  106, 110:     _loadVISlot().allowListEnabled = false;
  107, 111:     emit AllowListEnabled(false);
  108, 112:   }
@@ -111,7 +115,7 @@ abstract contract VaultImplementation is
  111, 115:    * @notice enable the allowList for the vault
  112, 116:    */
  113, 117:   function enableAllowList() external virtual {
- 114     :-    require(msg.sender == owner()); //owner is "strategist"
+      118:+    is_owner(); //owner is "strategist"
  115, 119:     _loadVISlot().allowListEnabled = true;
  116, 120:     emit AllowListEnabled(true);
  117, 121:   }
@@ -144,7 +148,7 @@ abstract contract VaultImplementation is
  144, 148:   }
  145, 149: 
  146, 150:   function shutdown() external {
- 147     :-    require(msg.sender == owner()); //owner is "strategist"
+      151:+    is_owner(); //owner is "strategist"
  148, 152:     _loadVISlot().isShutdown = true;
  149, 153:     emit VaultShutdown();
  150, 154:   }
@@ -208,7 +212,7 @@ abstract contract VaultImplementation is
  208, 212:   }
  209, 213: 
  210, 214:   function setDelegate(address delegate_) external {
- 211     :-    require(msg.sender == owner()); //owner is "strategist"
+      215:+    is_owner(); //owner is "strategist"
  212, 216:     VIData storage s = _loadVISlot();
  213, 217:     s.delegate = delegate_;
  214, 218:     emit DelegateUpdated(delegate_);
```

##### - src/WithdrawProxy.sol:[138](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L138)

```diff
diff --git a/src/WithdrawProxy.sol b/src/WithdrawProxy.sol
index 9906ec7..a398939 100644
--- a/src/WithdrawProxy.sol
+++ b/src/WithdrawProxy.sol
@@ -133,9 +133,9 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  133, 133:     public
  134, 134:     virtual
  135, 135:     override(ERC4626Cloned, IERC4626)
+      136:+    onlyVault
  136, 137:     returns (uint256 assets)
  137, 138:   {
- 138     :-    require(msg.sender == VAULT(), "only vault can mint");
  139, 139:     _mint(receiver, shares);
  140, 140:     return shares;
  141, 141:   }
```

---

#### 04. **Instead of using a modifier you can save gas using private view functions (9 instances)**

Deployment. Gas Saved: **460 617**

Minimum Method Call. Gas Saved: **-1 250**

Average Method Call. Gas Saved: **-1 627**

Maximum Method Call. Gas Saved: **-2 415**

Overall gas change: **11 164 (0.315%)**

##### - src/AstariaRouter.sol:[196](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L196)

```diff
diff --git a/src/AstariaRouter.sol b/src/AstariaRouter.sol
index c797baa..1517a56 100644
--- a/src/AstariaRouter.sol
+++ b/src/AstariaRouter.sol
@@ -130,9 +130,9 @@ contract AstariaRouter is
  130, 130:     payable
  131, 131:     virtual
  132, 132:     override
- 133     :-    validVault(address(vault))
  134, 133:     returns (uint256 amountIn)
  135, 134:   {
+      135:+    validVault(address(vault));
  136, 136:     return super.mint(vault, to, shares, maxAmountIn);
  137, 137:   }
  138, 138: 
@@ -146,9 +146,9 @@ contract AstariaRouter is
  146, 146:     payable
  147, 147:     virtual
  148, 148:     override
- 149     :-    validVault(address(vault))
  150, 149:     returns (uint256 sharesOut)
  151, 150:   {
+      151:+    validVault(address(vault));
  152, 152:     return super.deposit(vault, to, amount, minSharesOut);
  153, 153:   }
  154, 154: 
@@ -162,9 +162,9 @@ contract AstariaRouter is
  162, 162:     payable
  163, 163:     virtual
  164, 164:     override
- 165     :-    validVault(address(vault))
  166, 165:     returns (uint256 sharesOut)
  167, 166:   {
+      167:+    validVault(address(vault));
  168, 168:     return super.withdraw(vault, to, amount, maxSharesOut);
  169, 169:   }
  170, 170: 
@@ -178,9 +178,9 @@ contract AstariaRouter is
  178, 178:     payable
  179, 179:     virtual
  180, 180:     override
- 181     :-    validVault(address(vault))
  182, 181:     returns (uint256 amountOut)
  183, 182:   {
+      183:+    validVault(address(vault));
  184, 184:     return super.redeem(vault, to, shares, minAmountOut);
  185, 185:   }
  186, 186: 
@@ -189,15 +189,15 @@ contract AstariaRouter is
  189, 189:     uint256 shares,
  190, 190:     address receiver,
  191, 191:     uint64 epoch
- 192     :-  ) public virtual validVault(address(vault)) returns (uint256 assets) {
+      192:+  ) public virtual returns (uint256 assets) {
+      193:+    validVault(address(vault));
  193, 194:     return vault.redeemFutureEpoch(shares, receiver, msg.sender, epoch);
  194, 195:   }
  195, 196: 
- 196     :-  modifier validVault(address targetVault) {
+      197:+  function validVault(address targetVault) private view {
  197, 198:     if (!isValidVault(targetVault)) {
  198, 199:       revert InvalidVault(targetVault);
  199, 200:     }
- 200     :-    _;
  201, 201:   }
  202, 202: 
  203, 203:   function pullToken(
@@ -580,13 +580,13 @@ contract AstariaRouter is
  580, 580:   )
  581, 581:     external
  582, 582:     whenNotPaused
- 583     :-    validVault(msg.sender)
  584, 583:     returns (
  585, 584:       uint256,
  586, 585:       ILienToken.Stack[] memory,
  587, 586:       uint256
  588, 587:     )
  589, 588:   {
+      589:+    validVault(msg.sender);
  590, 590:     RouterStorage storage s = _loadRouterSlot();
  591, 591: 
  592, 592:     return
```

##### - src/CollateralToken.sol:[253](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L253), [265](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L265), [602](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L602)

```diff
diff --git a/src/CollateralToken.sol b/src/CollateralToken.sol
index c82b400..14861b6 100644
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol
@@ -250,7 +250,7 @@ contract CollateralToken is
  250, 250:     emit FileUpdated(what, data);
  251, 251:   }
  252, 252: 
- 253     :-  modifier releaseCheck(uint256 collateralId) {
+      253:+  function releaseCheck(uint256 collateralId) private view {
  254, 254:     CollateralStorage storage s = _loadCollateralSlot();
  255, 255: 
  256, 256:     if (s.LIEN_TOKEN.getCollateralState(collateralId) != bytes32(0)) {
@@ -259,19 +259,18 @@ contract CollateralToken is
  259, 259:     if (s.collateralIdToAuction[collateralId] != bytes32(0)) {
  260, 260:       revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
  261, 261:     }
- 262     :-    _;
  263, 262:   }
  264, 263: 
- 265     :-  modifier onlyOwner(uint256 collateralId) {
+      264:+  function onlyOwner(uint256 collateralId) private view {
  266, 265:     require(ownerOf(collateralId) == msg.sender);
- 267     :-    _;
  268, 266:   }
  269, 267: 
  270, 268:   function flashAction(
  271, 269:     IFlashAction receiver,
  272, 270:     uint256 collateralId,
  273, 271:     bytes calldata data
- 274     :-  ) external onlyOwner(collateralId) {
+      272:+  ) external {
+      273:+    onlyOwner(collateralId);
  275, 274:     address addr;
  276, 275:     uint256 tokenId;
  277, 276:     CollateralStorage storage s = _loadCollateralSlot();
@@ -330,9 +329,9 @@ contract CollateralToken is
  330, 329: 
  331, 330:   function releaseToAddress(uint256 collateralId, address releaseTo)
  332, 331:     public
- 333     :-    releaseCheck(collateralId)
- 334     :-    onlyOwner(collateralId)
  335, 332:   {
+      333:+    releaseCheck(collateralId);
+      334:+    onlyOwner(collateralId);
  336, 335:     CollateralStorage storage s = _loadCollateralSlot();
  337, 336: 
  338, 337:     if (msg.sender != ownerOf(collateralId)) {
@@ -555,7 +554,8 @@ contract CollateralToken is
  555, 554:     address from_,
  556, 555:     uint256 tokenId_,
  557, 556:     bytes calldata // calldata data_
- 558     :-  ) external override whenNotPaused returns (bytes4) {
+      557:+  ) external override returns (bytes4) {
+      558:+    whenNotPaused();
  559, 559:     CollateralStorage storage s = _loadCollateralSlot();
  560, 560:     uint256 collateralId = msg.sender.computeId(tokenId_);
  561, 561: 
@@ -599,10 +599,9 @@ contract CollateralToken is
  599, 599:     }
  600, 600:   }
  601, 601: 
- 602     :-  modifier whenNotPaused() {
+      602:+  function whenNotPaused() private view {
  603, 603:     if (_loadCollateralSlot().ASTARIA_ROUTER.paused()) {
  604, 604:       revert ProtocolPaused();
  605, 605:     }
- 606     :-    _;
  607, 606:   }
  608, 607: }
```

##### - src/LienToken.sol:[265](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L265)

```diff
diff --git a/src/LienToken.sol b/src/LienToken.sol
index 631ac02..9460a99 100644
--- a/src/LienToken.sol
+++ b/src/LienToken.sol
@@ -106,9 +106,9 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  106, 106: 
  107, 107:   function buyoutLien(ILienToken.LienActionBuyout calldata params)
  108, 108:     external
- 109     :-    validateStack(params.encumber.lien.collateralId, params.encumber.stack)
  110, 109:     returns (Stack[] memory, Stack memory newStack)
  111, 110:   {
+      111:+    validateStack(params.encumber.lien.collateralId, params.encumber.stack);
  112, 112:     if (block.timestamp >= params.encumber.stack[params.position].point.end) {
  113, 113:       revert InvalidState(InvalidStates.EXPIRED_LIEN);
  114, 114:     }
@@ -262,7 +262,7 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  262, 262:     return (delta_t * stack.lien.details.rate).mulWadDown(stack.point.amount);
  263, 263:   }
  264, 264: 
- 265     :-  modifier validateStack(uint256 collateralId, Stack[] memory stack) {
+      265:+  function validateStack(uint256 collateralId, Stack[] memory stack) private view {
  266, 266:     LienStorage storage s = _loadLienStorageSlot();
  267, 267:     bytes32 stateHash = s.collateralStateHash[collateralId];
  268, 268:     if (stateHash == bytes32(0) && stack.length != 0) {
@@ -271,7 +271,6 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  271, 271:     if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
  272, 272:       revert InvalidState(InvalidStates.INVALID_HASH);
  273, 273:     }
- 274     :-    _;
  275, 274:   }
  276, 275: 
  277, 276:   function stopLiens(
@@ -279,7 +278,8 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  279, 278:     uint256 auctionWindow,
  280, 279:     Stack[] calldata stack,
  281, 280:     address liquidator
- 282     :-  ) external validateStack(collateralId, stack) requiresAuth {
+      281:+  ) external requiresAuth {
+      282:+    validateStack(collateralId, stack);
  283, 283:     _stopLiens(
  284, 284:       _loadLienStorageSlot(),
  285, 285:       collateralId,
@@ -389,13 +389,13 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  389, 389:   function createLien(ILienToken.LienActionEncumber memory params)
  390, 390:     external
  391, 391:     requiresAuth
- 392     :-    validateStack(params.lien.collateralId, params.stack)
  393, 392:     returns (
  394, 393:       uint256 lienId,
  395, 394:       Stack[] memory newStack,
  396, 395:       uint256 lienSlope
  397, 396:     )
  398, 397:   {
+      398:+    validateStack(params.lien.collateralId, params.stack);
  399, 399:     LienStorage storage s = _loadLienStorageSlot();
  400, 400:     //0 - 4 are valid
  401, 401:     Stack memory newStackSlot;
@@ -599,9 +599,9 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  599, 599:     uint256 amount
  600, 600:   )
  601, 601:     public
- 602     :-    validateStack(collateralId, stack)
  603, 602:     returns (Stack[] memory newStack)
  604, 603:   {
+      604:+    validateStack(collateralId, stack);
  605, 605:     return _makePayment(_loadLienStorageSlot(), stack, amount);
  606, 606:   }
  607, 607: 
@@ -612,9 +612,9 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  612, 612:     uint256 amount
  613, 613:   )
  614, 614:     external
- 615     :-    validateStack(collateralId, stack)
  616, 615:     returns (Stack[] memory newStack)
  617, 616:   {
+      617:+    validateStack(collateralId, stack);
  618, 618:     LienStorage storage s = _loadLienStorageSlot();
  619, 619:     (newStack, ) = _payment(s, stack, position, amount, msg.sender);
  620, 620:     _updateCollateralStateHash(s, collateralId, newStack);
@@ -706,9 +706,9 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  706, 706:   function getMaxPotentialDebtForCollateral(Stack[] memory stack)
  707, 707:     public
  708, 708:     view
- 709     :-    validateStack(stack[0].lien.collateralId, stack)
  710, 709:     returns (uint256 maxPotentialDebt)
  711, 710:   {
+      711:+    validateStack(stack[0].lien.collateralId, stack);
  712, 712:     return _getMaxPotentialDebtForCollateralUpToNPositions(stack, stack.length);
  713, 713:   }
  714, 714: 
@@ -727,9 +727,9 @@ contract LienToken is ERC721, ILienToken, AuthInitializable {
  727, 727:   function getMaxPotentialDebtForCollateral(Stack[] memory stack, uint256 end)
  728, 728:     public
  729, 729:     view
- 730     :-    validateStack(stack[0].lien.collateralId, stack)
  731, 730:     returns (uint256 maxPotentialDebt)
  732, 731:   {
+      732:+    validateStack(stack[0].lien.collateralId, stack);
  733, 733:     uint256 i;
  734, 734:     for (; i < stack.length; ) {
  735, 735:       maxPotentialDebt += _getOwed(stack[i], end);
```

##### - src/PublicVault.sol:[679](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L679)

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..4ceb7ea 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -233,9 +233,9 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  233, 233:   function mint(uint256 shares, address receiver)
  234, 234:     public
  235, 235:     override(ERC4626Cloned)
- 236     :-    whenNotPaused
  237, 236:     returns (uint256)
  238, 237:   {
+      238:+    whenNotPaused();
  239, 239:     VIData storage s = _loadVISlot();
  240, 240:     if (s.allowListEnabled) {
  241, 241:       require(s.allowList[receiver]);
@@ -251,9 +251,9 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  251, 251:   function deposit(uint256 amount, address receiver)
  252, 252:     public
  253, 253:     override(ERC4626Cloned)
- 254     :-    whenNotPaused
  255, 254:     returns (uint256)
  256, 255:   {
+      256:+    whenNotPaused();
  257, 257:     VIData storage s = _loadVISlot();
  258, 258:     if (s.allowListEnabled) {
  259, 259:       require(s.allowList[receiver]);
@@ -514,8 +514,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  514, 514: 
  515, 515:   function beforePayment(BeforePaymentParams calldata params)
  516, 516:     external
- 517     :-    onlyLienToken
  518, 517:   {
+      518:+    onlyLienToken();
  519, 519:     VaultData storage s = _loadStorageSlot();
  520, 520:     _accrue(s);
  521, 521: 
@@ -531,7 +531,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  531, 531:     emit SlopeUpdated(newSlope);
  532, 532:   }
  533, 533: 
- 534     :-  function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {
+      534:+  function decreaseEpochLienCount(uint64 epoch) public {
+      535:+    onlyLienToken();
  535, 536:     _decreaseEpochLienCount(_loadStorageSlot(), epoch);
  536, 537:   }
  537, 538: 
@@ -559,7 +560,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  559, 560:     }
  560, 561:   }
  561, 562: 
- 562     :-  function afterPayment(uint256 computedSlope) public onlyLienToken {
+      563:+  function afterPayment(uint256 computedSlope) public {
+      564:+    onlyLienToken();
  563, 565:     VaultData storage s = _loadStorageSlot();
  564, 566:     unchecked {
  565, 567:       s.slope += computedSlope.safeCastTo48();
@@ -614,8 +616,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  614, 616: 
  615, 617:   function handleBuyoutLien(BuyoutLienParams calldata params)
  616, 618:     public
- 617     :-    onlyLienToken
  618, 619:   {
+      620:+    onlyLienToken();
  619, 621:     VaultData storage s = _loadStorageSlot();
  620, 622: 
  621, 623:     unchecked {
@@ -631,7 +633,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  631, 633: 
  632, 634:   function updateAfterLiquidationPayment(
  633, 635:     LiquidationPaymentParams calldata params
- 634     :-  ) external onlyLienToken {
+      636:+  ) external {
+      637:+    onlyLienToken();
  635, 638:     VaultData storage s = _loadStorageSlot();
  636, 639:     if (params.remaining > 0)
  637, 640:       _setYIntercept(s, s.yIntercept - params.remaining);
@@ -640,7 +643,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  640, 643:   function updateVaultAfterLiquidation(
  641, 644:     uint256 maxAuctionWindow,
  642, 645:     AfterLiquidationParams calldata params
- 643     :-  ) public onlyLienToken returns (address withdrawProxyIfNearBoundary) {
+      646:+  ) public returns (address withdrawProxyIfNearBoundary) {
+      647:+    onlyLienToken();
  644, 648:     VaultData storage s = _loadStorageSlot();
  645, 649: 
  646, 650:     _accrue(s);
@@ -676,9 +680,8 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
  676, 680:     _setYIntercept(s, s.yIntercept + amount);
  677, 681:   }
  678, 682: 
- 679     :-  modifier onlyLienToken() {
+      683:+  function onlyLienToken() private view {
  680, 684:     require(msg.sender == address(LIEN_TOKEN()));
- 681     :-    _;
  682, 685:   }
  683, 686: 
  684, 687:   function decreaseYIntercept(uint256 amount) public {
```

##### - src/VaultImplementation.sol:[131](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L131),

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..d1d1c1d 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -128,7 +128,7 @@ abstract contract VaultImplementation is
  128, 128:     return ERC721TokenReceiver.onERC721Received.selector;
  129, 129:   }
  130, 130: 
- 131     :-  modifier whenNotPaused() {
+      131:+  function whenNotPaused() internal view {
  132, 132:     if (ROUTER().paused()) {
  133, 133:       revert InvalidRequest(InvalidRequestReason.PAUSED);
  134, 134:     }
@@ -136,7 +136,6 @@ abstract contract VaultImplementation is
  136, 136:     if (_loadVISlot().isShutdown) {
  137, 137:       revert InvalidRequest(InvalidRequestReason.SHUTDOWN);
  138, 138:     }
- 139     :-    _;
  140, 139:   }
  141, 140: 
  142, 141:   function getShutdown() external view returns (bool) {
@@ -289,9 +288,9 @@ abstract contract VaultImplementation is
  289, 288:     address receiver
  290, 289:   )
  291, 290:     external
- 292     :-    whenNotPaused
  293, 291:     returns (uint256 lienId, ILienToken.Stack[] memory stack, uint256 payout)
  294, 292:   {
+      293:+    whenNotPaused();
  295, 294:     _beforeCommitToLien(params);
  296, 295:     uint256 slopeAddition;
  297, 296:     (lienId, stack, slopeAddition, payout) = _requestLienAndIssuePayout(
@@ -316,9 +315,9 @@ abstract contract VaultImplementation is
  316, 315:     IAstariaRouter.Commitment calldata incomingTerms
  317, 316:   )
  318, 317:     external
- 319     :-    whenNotPaused
  320, 318:     returns (ILienToken.Stack[] memory, ILienToken.Stack memory)
  321, 319:   {
+      320:+    whenNotPaused();
  322, 321:     LienToken lienToken = LienToken(address(ROUTER().LIEN_TOKEN()));
  323, 322: 
  324, 323:     (uint256 owed, uint256 buyout) = lienToken.getBuyout(stack[position]);
```

##### - src/WithdrawProxy.sol:[152](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L152), [230](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L230)

```diff
diff --git a/src/WithdrawProxy.sol b/src/WithdrawProxy.sol
index 9906ec7..711a8c6 100644
--- a/src/WithdrawProxy.sol
+++ b/src/WithdrawProxy.sol
@@ -149,7 +149,7 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  149, 149:     revert NotSupported();
  150, 150:   }
  151, 151: 
- 152     :-  modifier onlyWhenNoActiveAuction() {
+      152:+  function onlyWhenNoActiveAuction() private view {
  153, 153:     WPStorage storage s = _loadSlot();
  154, 154:     // If auction funds have been collected to the WithdrawProxy
  155, 155:     // but the PublicVault hasn't claimed its share, too much money will be sent to LPs
@@ -157,7 +157,6 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  157, 157:       // if finalAuctionEnd is 0, no auctions were added
  158, 158:       revert InvalidState(InvalidStates.NOT_CLAIMED);
  159, 159:     }
- 160     :-    _;
  161, 160:   }
  162, 161: 
  163, 162:   function withdraw(
@@ -168,9 +167,9 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  168, 167:     public
  169, 168:     virtual
  170, 169:     override(ERC4626Cloned, IERC4626)
- 171     :-    onlyWhenNoActiveAuction
  172, 170:     returns (uint256 shares)
  173, 171:   {
+      172:+    onlyWhenNoActiveAuction();
  174, 173:     return super.withdraw(assets, receiver, owner);
  175, 174:   }
  176, 175: 
@@ -189,9 +188,9 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  189, 188:     public
  190, 189:     virtual
  191, 190:     override(ERC4626Cloned, IERC4626)
- 192     :-    onlyWhenNoActiveAuction
  193, 191:     returns (uint256 assets)
  194, 192:   {
+      193:+    onlyWhenNoActiveAuction();
  195, 194:     return super.redeem(shares, receiver, owner);
  196, 195:   }
  197, 196: 
@@ -227,12 +226,12 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  227, 226:     return s.expected;
  228, 227:   }
  229, 228: 
- 230     :-  modifier onlyVault() {
+      229:+  function onlyVault() private view {
  231, 230:     require(msg.sender == VAULT(), "only vault can call");
- 232     :-    _;
  233, 231:   }
  234, 232: 
- 235     :-  function increaseWithdrawReserveReceived(uint256 amount) external onlyVault {
+      233:+  function increaseWithdrawReserveReceived(uint256 amount) external {
+      234:+    onlyVault();
  236, 235:     WPStorage storage s = _loadSlot();
  237, 236:     s.withdrawReserveReceived += amount;
  238, 237:   }
@@ -288,9 +287,9 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  288, 287: 
  289, 288:   function drain(uint256 amount, address withdrawProxy)
  290, 289:     public
- 291     :-    onlyVault
  292, 290:     returns (uint256)
  293, 291:   {
+      292:+    onlyVault();
  294, 293:     uint256 balance = ERC20(asset()).balanceOf(address(this));
  295, 294:     if (amount > balance) {
  296, 295:       amount = balance;
@@ -299,14 +298,16 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
  299, 298:     return amount;
  300, 299:   }
  301, 300: 
- 302     :-  function setWithdrawRatio(uint256 liquidationWithdrawRatio) public onlyVault {
+      301:+  function setWithdrawRatio(uint256 liquidationWithdrawRatio) public {
+      302:+    onlyVault();
  303, 303:     _loadSlot().withdrawRatio = liquidationWithdrawRatio.safeCastTo88();
  304, 304:   }
  305, 305: 
  306, 306:   function handleNewLiquidation(
  307, 307:     uint256 newLienExpectedValue,
  308, 308:     uint256 finalAuctionDelta
- 309     :-  ) public onlyVault {
+      309:+  ) public {
+      310:+    onlyVault();
  310, 311:     WPStorage storage s = _loadSlot();
  311, 312: 
  312, 313:     unchecked {
```

---

#### 99. **Overall gas savings**

Deployment. Gas Saved: **622 011**

Minimum Method Call. Gas Saved: **689**

Average Method Call. Gas Saved: **1 177**

Maximum Method Call. Gas Saved: **2 057**

Overall gas change: **-11 465 (-0.234%)**

**NOTE**: Result after merging all findings in one commit


```diff
 | src/AstariaRouter.sol:AstariaRouter contract |                 |        |        |         |         |
 |----------------------------------------------|-----------------|--------|--------|---------|---------|
 | Deployment Cost                              | Deployment Size |        |        |         |         |
-| 4897162                                      | 24682           |        |        |         |         |
+| 4835881                                      | 24376           |        |        |         |         |
 | Function Name                                | min             | avg    | median | max     | # calls |
 | BEACON_PROXY_IMPLEMENTATION                  | 601             | 658    | 601    | 2601    | 70      |
 | COLLATERAL_TOKEN                             | 598             | 598    | 598    | 598     | 130     |
 | LIEN_TOKEN                                   | 600             | 600    | 600    | 600     | 83      |
 | TRANSFER_PROXY                               | 534             | 534    | 534    | 534     | 14      |
-| commitToLiens                                | 110806          | 409679 | 446263 | 1350578 | 47      |
-| depositToVault                               | 64239           | 136633 | 153704 | 153704  | 51      |
+| commitToLiens                                | 110888          | 409698 | 446275 | 1350878 | 47      |
+| depositToVault                               | 64284           | 136677 | 153749 | 153749  | 51      |
 | feeTo                                        | 513             | 1876   | 2513   | 2513    | 44      |
 | file                                         | 18408           | 18408  | 18408  | 18408   | 1       |
 | fileBatch                                    | 84449           | 84449  | 84449  | 84449   | 45      |
@@ -77,14 +973,14 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | getImpl                                      | 776             | 938    | 776    | 2776    | 1132    |
 | getLiquidatorFee                             | 800             | 800    | 800    | 800     | 12      |
 | initialize                                   | 318323          | 318323 | 318323 | 318323  | 45      |
-| isValidRefinance                             | 3926            | 3926   | 3926   | 3926    | 1       |
+| isValidRefinance                             | 3921            | 3921   | 3921   | 3921    | 1       |
 | isValidVault                                 | 760             | 1216   | 760    | 2760    | 57      |
-| liquidate                                    | 343339          | 452390 | 392144 | 1094724 | 17      |
+| liquidate                                    | 342775          | 451779 | 391604 | 1094352 | 17      |
 | newPublicVault                               | 5545            | 133768 | 142450 | 142450  | 45      |
 | newVault                                     | 156765          | 161407 | 163265 | 163265  | 7       |
 | paused                                       | 573             | 600    | 573    | 2573    | 145     |
-| redeemFutureEpoch                            | 33318           | 128175 | 134662 | 138262  | 21      |
-| requestLienPosition                          | 27763           | 126381 | 146211 | 169088  | 51      |
+| redeemFutureEpoch                            | 33360           | 128217 | 134704 | 138304  | 21      |
+| requestLienPosition                          | 27789           | 126428 | 146259 | 169136  | 51      |
 | validateCommitment                           | 20701           | 20701  | 20701  | 20701   | 2       |
 
 
@@ -93,19 +989,19 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | Deployment Cost                          | Deployment Size |        |        |        |         |
 | 78529                                    | 424             |        |        |        |         |
 | Function Name                            | min             | avg    | median | max    | # calls |
-| afterPayment                             | 6443            | 6443   | 6443   | 6443   | 3       |
-| approve(address,uint256)                 | 27177           | 27191  | 27191  | 27205  | 10      |
-| approve(address,uint256)(bool)           | 27177           | 27191  | 27191  | 27205  | 32      |
+| afterPayment                             | 6467            | 6467   | 6467   | 6467   | 3       |
+| approve(address,uint256)                 | 27177           | 27191  | 27191  | 27205  | 14      |
+| approve(address,uint256)(bool)           | 27177           | 27191  | 27191  | 27205  | 28      |
 | asset                                    | 2951            | 3006   | 3009   | 3009   | 194     |
 | balanceOf                                | 3274            | 3294   | 3274   | 3323   | 60      |
-| beforePayment                            | 9380            | 9380   | 9380   | 9380   | 9       |
-| buyoutLien                               | 101566          | 143725 | 143725 | 185884 | 2       |
+| beforePayment                            | 9404            | 9404   | 9404   | 9404   | 9       |
+| buyoutLien                               | 101605          | 143771 | 143771 | 185938 | 2       |
 | claim                                    | 7160            | 18055  | 18577  | 24615  | 13      |
-| commitToLien                             | 58256           | 199221 | 216231 | 247499 | 51      |
-| decreaseEpochLienCount                   | 5784            | 6024   | 5784   | 7229   | 6       |
+| commitToLien                             | 58306           | 199286 | 216303 | 247571 | 51      |
+| decreaseEpochLienCount                   | 5803            | 6044   | 5803   | 7253   | 6       |
 | decreaseYIntercept                       | 5140            | 5140   | 5140   | 5140   | 3       |
-| deposit                                  | 5261            | 72839  | 88656  | 88656  | 55      |
-| drain                                    | 9778            | 18724  | 18724  | 27670  | 2       |
+| deposit                                  | 5261            | 72856  | 88675  | 88675  | 55      |
+| drain                                    | 9802            | 18748  | 18748  | 27694  | 2       |
 | getCurrentEpoch                          | 3086            | 3086   | 3086   | 3086   | 51      |
 | getEpochEnd                              | 3575            | 3575   | 3575   | 3575   | 21      |
 | getExpected                              | 3028            | 3028   | 3028   | 3028   | 20      |
@@ -118,67 +1014,67 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | getWithdrawProxy                         | 3374            | 3374   | 3374   | 3374   | 44      |
 | getWithdrawReserve                       | 3087            | 3087   | 3087   | 3087   | 5       |
 | getYIntercept                            | 3060            | 3060   | 3060   | 3060   | 6       |
-| handleBuyoutLien                         | 9201            | 9201   | 9201   | 9201   | 1       |
-| handleNewLiquidation                     | 3813            | 24413  | 25960  | 30460  | 17      |
-| increaseWithdrawReserveReceived          | 3497            | 17549  | 23397  | 25397  | 17      |
+| handleBuyoutLien                         | 9220            | 9220   | 9220   | 9220   | 1       |
+| handleNewLiquidation                     | 3837            | 24437  | 25984  | 30484  | 17      |
+| increaseWithdrawReserveReceived          | 3521            | 17573  | 23421  | 25421  | 17      |
 | increaseYIntercept                       | 5207            | 5207   | 5207   | 5207   | 8       |
 | incrementNonce                           | 3396            | 11542  | 4739   | 26491  | 3       |
 | init                                     | 26309           | 33355  | 30809  | 54139  | 50      |
-| mint                                     | 27728           | 51156  | 54128  | 54128  | 21      |
+| mint                                     | 27752           | 51180  | 54152  | 54152  | 21      |
 | onERC721Received                         | 3372            | 6930   | 7872   | 7872   | 43      |
 | previewRedeem                            | 4448            | 4448   | 4448   | 4448   | 1       |
-| processEpoch                             | 4093            | 35145  | 35883  | 70923  | 24      |
-| redeem                                   | 26970           | 27060  | 26970  | 27206  | 13      |
-| redeemFutureEpoch                        | 31904           | 126761 | 133248 | 136848 | 21      |
-| safeTransferFrom                         | 64190           | 103154 | 86510  | 211805 | 12      |
+| processEpoch                             | 4093            | 34977  | 35749  | 70789  | 24      |
+| redeem                                   | 26986           | 27076  | 26986  | 27222  | 13      |
+| redeemFutureEpoch                        | 31923           | 126780 | 133267 | 136867 | 21      |
+| safeTransferFrom                         | 64190           | 103164 | 86510  | 211824 | 12      |
 | setAuctionData                           | 117784          | 138862 | 117784 | 296949 | 17      |
-| setWithdrawRatio                         | 3456            | 12116  | 3456   | 25356  | 20      |
-| settleLiquidatorNFTClaim                 | 27267           | 27267  | 27267  | 27267  | 1       |
-| shutdown                                 | 26041           | 26041  | 26041  | 26041  | 1       |
+| setWithdrawRatio                         | 3480            | 12140  | 3480   | 25380  | 20      |
+| settleLiquidatorNFTClaim                 | 27295           | 27295  | 27295  | 27295  | 1       |
+| shutdown                                 | 26065           | 26065  | 26065  | 26065  | 1       |
 | supportsInterface                        | 2885            | 3058   | 3062   | 3062   | 93      |
 | timeToSecondEpochEnd                     | 4058            | 4058   | 4058   | 4058   | 49      |
 | totalAssets                              | 3501            | 3501   | 3501   | 3501   | 8       |
 | totalSupply                              | 3038            | 3520   | 3038   | 5038   | 23      |
-| transferUnderlying                       | 8737            | 33902  | 24657  | 68312  | 3       |
-| transferWithdrawReserve                  | 14268           | 37394  | 36168  | 69028  | 13      |
-| updateAfterLiquidationPayment            | 5031            | 5506   | 5031   | 6695   | 7       |
-| updateVaultAfterLiquidation              | 10502           | 49410  | 40120  | 105672 | 25      |
-| validateOrder                            | 75748           | 82748  | 84248  | 84248  | 17      |
+| transferUnderlying                       | 8746            | 33910  | 24666  | 68320  | 3       |
+| transferWithdrawReserve                  | 14062           | 37136  | 35962  | 67830  | 13      |
+| updateAfterLiquidationPayment            | 5055            | 5530   | 5055   | 6719   | 7       |
+| updateVaultAfterLiquidation              | 10521           | 49393  | 40168  | 105720 | 25      |
+| validateOrder                            | 75759           | 82759  | 84259  | 84259  | 17      |
 
 
 | src/ClearingHouse.sol:ClearingHouse contract |                 |        |        |        |         |
 |----------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                              | Deployment Size |        |        |        |         |
-| 1726613                                      | 8656            |        |        |        |         |
+| 1701785                                      | 8532            |        |        |        |         |
 | Function Name                                | min             | avg    | median | max    | # calls |
 | onERC721Received                             | 852             | 852    | 852    | 852    | 43      |
-| safeTransferFrom                             | 62172           | 101136 | 84492  | 209787 | 12      |
+| safeTransferFrom                             | 62172           | 101146 | 84492  | 209806 | 12      |
 | setAuctionData                               | 115231          | 136300 | 115231 | 294323 | 17      |
-| settleLiquidatorNFTClaim                     | 25268           | 25268  | 25268  | 25268  | 1       |
-| transferUnderlying                           | 6731            | 31895  | 22651  | 66305  | 3       |
-| validateOrder                                | 73055           | 80055  | 81555  | 81555  | 17      |
+| settleLiquidatorNFTClaim                     | 25296           | 25296  | 25296  | 25296  | 1       |
+| transferUnderlying                           | 6740            | 31904  | 22660  | 66314  | 3       |
+| validateOrder                                | 73066           | 80066  | 81566  | 81566  | 17      |
 
 
 | src/CollateralToken.sol:CollateralToken contract |                 |        |        |        |         |
 |--------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                  | Deployment Size |        |        |        |         |
-| 3502498                                          | 17718           |        |        |        |         |
+| 3478874                                          | 17600           |        |        |        |         |
 | Function Name                                    | min             | avg    | median | max    | # calls |
 | SEAPORT                                          | 562             | 2209   | 2562   | 2562   | 17      |
-| auctionVault                                     | 124112          | 139347 | 142612 | 142612 | 17      |
+| auctionVault                                     | 123500          | 138735 | 142000 | 142000 | 17      |
 | file                                             | 23370           | 32087  | 32087  | 40804  | 2       |
 | fileBatch                                        | 32740           | 34671  | 34671  | 36603  | 90      |
-| flashAction                                      | 267623          | 267623 | 267623 | 267623 | 1       |
+| flashAction                                      | 267374          | 267374 | 267374 | 267374 | 1       |
 | getApproved                                      | 725             | 2272   | 2725   | 2725   | 53      |
 | getClearingHouse                                 | 656             | 656    | 656    | 656    | 30      |
 | getConduit                                       | 523             | 974    | 523    | 2523   | 62      |
 | initialize                                       | 973690          | 973690 | 973690 | 973690 | 45      |
 | isApprovedForAll                                 | 952             | 952    | 952    | 952    | 51      |
-| liquidatorNFTClaim                               | 45980           | 45980  | 45980  | 45980  | 1       |
-| onERC721Received                                 | 166273          | 183609 | 176773 | 215828 | 43      |
+| liquidatorNFTClaim                               | 45876           | 45876  | 45876  | 45876  | 1       |
+| onERC721Received                                 | 166213          | 183549 | 176713 | 215780 | 43      |
 | owner                                            | 556             | 556    | 556    | 556    | 45      |
 | ownerOf                                          | 741             | 741    | 741    | 741    | 167     |
-| releaseToAddress                                 | 34532           | 34532  | 34532  | 34532  | 1       |
+| releaseToAddress                                 | 34580           | 34580  | 34580  | 34580  | 1       |
 | securityHooks                                    | 825             | 825    | 825    | 825    | 1       |
 | setApprovalForAll                                | 2807            | 18649  | 24707  | 24707  | 47      |
 | settleAuction                                    | 3670            | 3670   | 3670   | 3670   | 12      |
@@ -187,11 +1083,11 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | src/LienToken.sol:LienToken contract                                                                                                        |                 |        |        |        |         |
 |---------------------------------------------------------------------------------------------------------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                                                                                                                             | Deployment Size |        |        |        |         |
-| 4616771                                                                                                                                     | 23282           |        |        |        |         |
+| 4413300                                                                                                                                     | 22266           |        |        |        |         |
 | Function Name                                                                                                                               | min             | avg    | median | max    | # calls |
 | COLLATERAL_TOKEN                                                                                                                            | 604             | 604    | 604    | 604    | 1       |
-| buyoutLien                                                                                                                                  | 4558            | 54346  | 54346  | 104134 | 2       |
-| createLien                                                                                                                                  | 8403            | 84971  | 98406  | 102906 | 49      |
+| buyoutLien                                                                                                                                  | 4573            | 54370  | 54370  | 104168 | 2       |
+| createLien                                                                                                                                  | 8418            | 84996  | 98430  | 102930 | 49      |
 | file                                                                                                                                        | 20769           | 31078  | 28218  | 34169  | 91      |
 | getAuctionLiquidator                                                                                                                        | 736             | 736    | 736    | 736    | 1       |
 | getBuyout                                                                                                                                   | 11024           | 11024  | 11024  | 11024  | 2       |
@@ -200,26 +1096,26 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | getOwed(((uint8,address,address,bytes32,uint256,(uint256,uint256,uint256,uint256,uint256)),(uint88,uint40,uint40,uint256)),uint256)(uint88) | 3571            | 3571   | 3571   | 3571   | 2       |
 | getPayee                                                                                                                                    | 1373            | 1972   | 1472   | 3572   | 4       |
 | initialize                                                                                                                                  | 161943          | 161943 | 161943 | 161943 | 45      |
-| makePayment                                                                                                                                 | 3989            | 43234  | 47817  | 51659  | 10      |
-| payDebtViaClearingHouse                                                                                                                     | 17459           | 51862  | 39779  | 163873 | 13      |
-| stopLiens                                                                                                                                   | 186472          | 297791 | 235277 | 929383 | 17      |
+| makePayment                                                                                                                                 | 4004            | 43290  | 47875  | 51731  | 10      |
+| payDebtViaClearingHouse                                                                                                                     | 17459           | 51872  | 39779  | 163892 | 13      |
+| stopLiens                                                                                                                                   | 186520          | 297792 | 235349 | 929623 | 17      |
 
 
 | src/PublicVault.sol:PublicVault contract |                 |        |        |        |         |
 |------------------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost                          | Deployment Size |        |        |        |         |
-| 4832294                                  | 24166           |        |        |        |         |
+| 4620401                                  | 23108           |        |        |        |         |
 | Function Name                            | min             | avg    | median | max    | # calls |
-| afterPayment                             | 3926            | 3926   | 3926   | 3926   | 3       |
-| approve(address,uint256)                 | 24679           | 24679  | 24679  | 24679  | 5       |
-| approve(address,uint256)(bool)           | 24679           | 24679  | 24679  | 24679  | 16      |
+| afterPayment                             | 3950            | 3950   | 3950   | 3950   | 3       |
+| approve(address,uint256)                 | 24679           | 24679  | 24679  | 24679  | 7       |
+| approve(address,uint256)(bool)           | 24679           | 24679  | 24679  | 24679  | 14      |
 | asset                                    | 495             | 495    | 495    | 495    | 184     |
 | balanceOf                                | 803             | 803    | 803    | 803    | 25      |
-| beforePayment                            | 6851            | 6851   | 6851   | 6851   | 9       |
-| commitToLien                             | 55562           | 195597 | 213233 | 244294 | 49      |
-| decreaseEpochLienCount                   | 3770            | 3927   | 3770   | 4712   | 6       |
+| beforePayment                            | 6875            | 6875   | 6875   | 6875   | 9       |
+| commitToLien                             | 55612           | 195662 | 213305 | 244312 | 49      |
+| decreaseEpochLienCount                   | 3789            | 3946   | 3789   | 4736   | 6       |
 | decreaseYIntercept                       | 2623            | 2623   | 2623   | 2623   | 3       |
-| deposit                                  | 14049           | 77565  | 86635  | 86635  | 49      |
+| deposit                                  | 14068           | 77584  | 86654  | 86654  | 49      |
 | getCurrentEpoch                          | 572             | 572    | 572    | 572    | 51      |
 | getEpochEnd                              | 1055            | 1055   | 1055   | 1055   | 21      |
 | getLienEpoch                             | 1176            | 1176   | 1176   | 1176   | 6       |
@@ -230,20 +1126,20 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | getWithdrawProxy                         | 854             | 854    | 854    | 854    | 44      |
 | getWithdrawReserve                       | 573             | 573    | 573    | 573    | 5       |
 | getYIntercept                            | 546             | 546    | 546    | 546    | 6       |
-| handleBuyoutLien                         | 7178            | 7178   | 7178   | 7178   | 1       |
+| handleBuyoutLien                         | 7197            | 7197   | 7197   | 7197   | 1       |
 | increaseYIntercept                       | 2690            | 2690   | 2690   | 2690   | 8       |
 | incrementNonce                           | 878             | 878    | 878    | 878    | 1       |
 | init                                     | 23762           | 23762  | 23762  | 23762  | 43      |
-| processEpoch                             | 1575            | 32884  | 33874  | 68914  | 24      |
-| redeemFutureEpoch                        | 29873           | 124730 | 131217 | 134817 | 21      |
-| shutdown                                 | 23530           | 23530  | 23530  | 23530  | 1       |
+| processEpoch                             | 1575            | 32716  | 33740  | 68780  | 24      |
+| redeemFutureEpoch                        | 29892           | 124749 | 131236 | 134836 | 21      |
+| shutdown                                 | 23554           | 23554  | 23554  | 23554  | 1       |
 | supportsInterface                        | 542             | 542    | 542    | 542    | 91      |
 | timeToSecondEpochEnd                     | 1544            | 1544   | 1544   | 1544   | 49      |
 | totalAssets                              | 987             | 987    | 987    | 987    | 8       |
 | totalSupply                              | 887             | 887    | 887    | 887    | 3       |
-| transferWithdrawReserve                  | 11757           | 35191  | 33657  | 66517  | 13      |
-| updateAfterLiquidationPayment            | 2514            | 2989   | 2514   | 4178   | 7       |
-| updateVaultAfterLiquidation              | 8472            | 47015  | 37582  | 103134 | 25      |
+| transferWithdrawReserve                  | 11551           | 34934  | 33451  | 65319  | 13      |
+| updateAfterLiquidationPayment            | 2538            | 3013   | 2538   | 4202   | 7       |
+| updateVaultAfterLiquidation              | 8491            | 46997  | 37630  | 103182 | 25      |
 
 
 | src/TransferProxy.sol:TransferProxy contract |                 |       |        |       |         |
@@ -257,11 +1153,11 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | src/Vault.sol:Vault contract |                 |        |        |        |         |
 |------------------------------|-----------------|--------|--------|--------|---------|
 | Deployment Cost              | Deployment Size |        |        |        |         |
-| 2213960                      | 11090           |        |        |        |         |
+| 2170309                      | 10872           |        |        |        |         |
 | Function Name                | min             | avg    | median | max    | # calls |
 | asset                        | 437             | 437    | 437    | 437    | 10      |
-| buyoutLien                   | 98686           | 141100 | 141100 | 183514 | 2       |
-| commitToLien                 | 205199          | 216852 | 216852 | 228506 | 2       |
+| buyoutLien                   | 98725           | 141146 | 141146 | 183568 | 2       |
+| commitToLien                 | 205271          | 216924 | 216924 | 228578 | 2       |
 | deposit                      | 2737            | 15551  | 21958  | 21958  | 6       |
 | incrementNonce               | 2228            | 13104  | 13104  | 23980  | 2       |
 | init                         | 47086           | 47086  | 47086  | 47086  | 7       |
@@ -271,21 +1167,21 @@ Test result: [32mok[0m. 21 passed; 0 failed; finished in 16.29s
 | src/WithdrawProxy.sol:WithdrawProxy contract |                 |       |        |       |         |
 |----------------------------------------------|-----------------|-------|--------|-------|---------|
 | Deployment Cost                              | Deployment Size |       |        |       |         |
-| 1673757                                      | 8392            |       |        |       |         |
+| 1620494                                      | 8126            |       |        |       |         |
 | Function Name                                | min             | avg   | median | max   | # calls |
-| approve(address,uint256)                     | 24666           | 24666 | 24666  | 24666 | 5       |
-| approve(address,uint256)(bool)               | 24666           | 24666 | 24666  | 24666 | 16      |
+| approve(address,uint256)                     | 24666           | 24666 | 24666  | 24666 | 7       |
+| approve(address,uint256)(bool)               | 24666           | 24666 | 24666  | 24666 | 14      |
 | balanceOf                                    | 766             | 766   | 766    | 766   | 35      |
 | claim                                        | 4651            | 15744 | 16576  | 22113 | 13      |
-| drain                                        | 7267            | 16213 | 16213  | 25159 | 2       |
+| drain                                        | 7291            | 16237 | 16237  | 25183 | 2       |
 | getExpected                                  | 523             | 523   | 523    | 523   | 20      |
 | getFinalAuctionEnd                           | 511             | 636   | 511    | 2511  | 16      |
-| handleNewLiquidation                         | 1305            | 20846 | 23452  | 23452 | 17      |
-| increaseWithdrawReserveReceived              | 992             | 15044 | 20892  | 22892 | 17      |
-| mint                                         | 25217           | 46074 | 47117  | 47117 | 21      |
+| handleNewLiquidation                         | 1329            | 20870 | 23476  | 23476 | 17      |
+| increaseWithdrawReserveReceived              | 1016            | 15068 | 20916  | 22916 | 17      |
+| mint                                         | 25241           | 46098 | 47141  | 47141 | 21      |
 | previewRedeem                                | 1940            | 1940  | 1940   | 1940  | 1       |
-| redeem                                       | 24959           | 25049 | 24959  | 25195 | 13      |
-| setWithdrawRatio                             | 951             | 9611  | 951    | 22851 | 20      |
+| redeem                                       | 24975           | 25065 | 24975  | 25211 | 13      |
+| setWithdrawRatio                             | 975             | 9635  | 975    | 22875 | 20      |
 | totalSupply                                  | 533             | 1033  | 533    | 2533  | 20      |
```