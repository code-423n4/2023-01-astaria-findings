### Table of contents
- [FINDINGS](#findings)
- [Notes on Gas estimates.](#notes-on-gas-estimates)
- [Pack structs by putting variables that can fit together next to each other](#pack-structs-by-putting-variables-that-can-fit-together-next-to-each-other)
  - [StrategyDetailsParam: version and vault can be packed together: `Gas saved: 1 * 2k = 2k`](#strategydetailsparam-version-and-vault-can-be-packed-together-gas-saved-1--2k--2k)
- [The result of a function call should be cached rather than re-calling the function](#the-result-of-a-function-call-should-be-cached-rather-than-re-calling-the-function)
  - [WithdrawProxy.sol.claim(): Results of VAULT() and asset() should be cached (Saves 434 gas)](#withdrawproxysolclaim-results-of-vault-and-asset-should-be-cached-saves-434-gas)
  - [WithdrawProxy.sol.drain(): Result of asset() should be cached](#withdrawproxysoldrain-result-of-asset-should-be-cached)
  - [PublicVault.sol.minDepositAmount(): ERC20(asset()).decimals() should be cached rather than call it twice](#publicvaultsolmindepositamount-erc20assetdecimals-should-be-cached-rather-than-call-it-twice)
  - [PublicVault.sol.\_deployWithdrawProxyIfNotDeployed(): ROUTER() should be cached](#publicvaultsol_deploywithdrawproxyifnotdeployed-router-should-be-cached)
  - [PublicVault.sol.processEpoch(): totalAssets() should be cached](#publicvaultsolprocessepoch-totalassets-should-be-cached)
  - [PublicVault.sol.transferWithdrawReserve(): Result of asset() should be cached](#publicvaultsoltransferwithdrawreserve-result-of-asset-should-be-cached)
  - [PublicVault.sol.\_handleStrategistInterestReward(): VAULT\_FEE() should be cached](#publicvaultsol_handlestrategistinterestreward-vault_fee-should-be-cached)
  - [VaultImplementation.sol.buyoutLien(): ROUTER() should be cached](#vaultimplementationsolbuyoutlien-router-should-be-cached)
  - [VaultImplementation.sol.buyoutLien(): asset() should be cached](#vaultimplementationsolbuyoutlien-asset-should-be-cached)
  - [VaultImplementation.sol.\_handleProtocolFee(): ROUTER() should be cached(happy path)](#vaultimplementationsol_handleprotocolfee-router-should-be-cachedhappy-path)
- [Internal/Private functions only called once can be inlined to save gas  `Gas saved: 20 * 20= 400`](#internalprivate-functions-only-called-once-can-be-inlined-to-save-gas--gas-saved-20--20-400)
- [Using storage instead of memory for structs/arrays saves gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas)
- [require() or revert() statements that check input arguments should be at the top of the function(Also restructured some if's)](#require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-functionalso-restructured-some-ifs)
- [`keccak256()` should only need to be called on a specific string literal once](#keccak256-should-only-need-to-be-called-on-a-specific-string-literal-once)
- [x += y costs more gas than x = x + y for state variables](#x--y-costs-more-gas-than-x--x--y-for-state-variables)
- [Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead](#usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)
- [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [Duplicated require()/revert() checks should be refactored to a modifier or function. see docs](#duplicated-requirerevert-checks-should-be-refactored-to-a-modifier-or-function-see-docs)
- [A modifier used only once and not being inherited should be inlined to save gas](#a-modifier-used-only-once-and-not-being-inherited-should-be-inlined-to-save-gas)

## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Through out the report some places might be denoted with audit tags to show the actual place affected.

## Notes on Gas estimates.
I've tried to give the exact amount of gas being saved from running the included tests. Whenever the function is within the test coverage, the average gas before and after will be included, and often a diff of the code after proposed changes will be given

Some functions are not covered by the test cases or are internal/private functions.

## Pack structs by putting variables that can fit together next to each other

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L101-L105
### StrategyDetailsParam: version and vault can be packed together: `Gas saved: 1 * 2k = 2k`

```solidity
File: /src/interfaces/IAstariaRouter.sol
101:  struct StrategyDetailsParam {
102:    uint8 version;
103:    uint256 deadline;
104:    address vault;
105:  }
```

```diff
diff --git a/src/interfaces/IAstariaRouter.sol b/src/interfaces/IAstariaRouter.sol
index 2ae1431..679f46a 100644
--- a/src/interfaces/IAstariaRouter.sol
+++ b/src/interfaces/IAstariaRouter.sol
@@ -100,8 +100,8 @@ interface IAstariaRouter is IPausable, IBeacon {

   struct StrategyDetailsParam {
     uint8 version;
-    uint256 deadline;
     address vault;
+    uint256 deadline;
   }
```


## The result of a function call should be cached rather than re-calling the function

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L240-L287
### WithdrawProxy.sol.claim(): Results of VAULT() and asset() should be cached (Saves 434 gas)
**Gas benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 4651    | 15745   | 16576 | 22114 |
| After  | 4854    | 15311   | 16073 | 21304 |

```solidity
File: /src/WithdrawProxy.sol
240:  function claim() public {

247:    if (PublicVault(VAULT()).getCurrentEpoch() < CLAIMABLE_EPOCH()) {
248:      revert InvalidState(InvalidStates.PROCESS_EPOCH_NOT_COMPLETE);
249:    }

255:    uint256 balance = ERC20(asset()).balanceOf(address(this)) -

258:    if (balance < s.expected) {
259:      PublicVault(VAULT()).decreaseYIntercept(
260:        (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
261:      );
262:    } else {
263:      PublicVault(VAULT()).increaseYIntercept(
264:        (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
265:      );
266:    }

268:    if (s.withdrawRatio == uint256(0)) {
269:      ERC20(asset()).safeTransfer(VAULT(), balance);
270:    } else {
271:      transferAmount = uint256(s.withdrawRatio).mulDivDown(
272:        balance,
273:        10**ERC20(asset()).decimals()
274:      );

280:      if (balance > 0) {
281:        ERC20(asset()).safeTransfer(VAULT(), balance);
282:      }
283:    }
284:    s.finalAuctionEnd = 0;

286:    emit Claimed(address(this), transferAmount, VAULT(), balance);
287:  }
```

```diff
diff --git a/src/WithdrawProxy.sol b/src/WithdrawProxy.sol
index 9906ec7..abe0255 100644
--- a/src/WithdrawProxy.sol
+++ b/src/WithdrawProxy.sol
@@ -243,8 +243,10 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
     if (s.finalAuctionEnd == 0) {
       revert InvalidState(InvalidStates.CANT_CLAIM);
     }
+    address _vault = VAULT();
+    address _asset = asset();

-    if (PublicVault(VAULT()).getCurrentEpoch() < CLAIMABLE_EPOCH()) {
+    if (PublicVault(_vault).getCurrentEpoch() < CLAIMABLE_EPOCH()) {
       revert InvalidState(InvalidStates.PROCESS_EPOCH_NOT_COMPLETE);
     }
     if (block.timestamp < s.finalAuctionEnd) {
@@ -252,25 +254,25 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
     }

     uint256 transferAmount = 0;
-    uint256 balance = ERC20(asset()).balanceOf(address(this)) -
+    uint256 balance = ERC20(_asset).balanceOf(address(this)) -
       s.withdrawReserveReceived; // will never underflow because withdrawReserveReceived is always increased by the transfer amount from the PublicVault

     if (balance < s.expected) {
-      PublicVault(VAULT()).decreaseYIntercept(
+      PublicVault(_vault).decreaseYIntercept(
         (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
       );
     } else {
-      PublicVault(VAULT()).increaseYIntercept(
+      PublicVault(_vault).increaseYIntercept(
         (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
       );
     }

     if (s.withdrawRatio == uint256(0)) {
-      ERC20(asset()).safeTransfer(VAULT(), balance);
+      ERC20(_asset).safeTransfer(_vault, balance);
     } else {
       transferAmount = uint256(s.withdrawRatio).mulDivDown(
         balance,
-        10**ERC20(asset()).decimals()
+        10**ERC20(_asset).decimals()
       );

       unchecked {
@@ -278,12 +280,12 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
       }

       if (balance > 0) {
-        ERC20(asset()).safeTransfer(VAULT(), balance);
+        ERC20(_asset).safeTransfer(_vault, balance);
       }
     }
     s.finalAuctionEnd = 0;

-    emit Claimed(address(this), transferAmount, VAULT(), balance);
+    emit Claimed(address(this), transferAmount, _vault, balance);
   }

```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L289-L300
### WithdrawProxy.sol.drain(): Result of asset() should be cached
**Saves  177 Gas on average**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 7268    | 16214   | 16214 | 25160 |
| After  | 7091    | 16037   | 16037 | 24983 |

```solidity
File: /src/WithdrawProxy.sol
289:  function drain(uint256 amount, address withdrawProxy)

293: {
294:    uint256 balance = ERC20(asset()).balanceOf(address(this));
295:    if (amount > balance) {
296:      amount = balance;
297:    }
298:    ERC20(asset()).safeTransfer(withdrawProxy, amount);
299:    return amount;
300:  }
```

```diff
diff --git a/src/WithdrawProxy.sol b/src/WithdrawProxy.sol
index 9906ec7..03ea25f 100644
--- a/src/WithdrawProxy.sol
+++ b/src/WithdrawProxy.sol
@@ -291,11 +291,12 @@ contract WithdrawProxy is ERC4626Cloned, WithdrawVaultBase {
     onlyVault
     returns (uint256)
   {
-    uint256 balance = ERC20(asset()).balanceOf(address(this));
+    address _asset = asset();
+    uint256 balance = ERC20(_asset).balanceOf(address(this));
     if (amount > balance) {
       amount = balance;
     }
-    ERC20(asset()).safeTransfer(withdrawProxy, amount);
+    ERC20(_asset).safeTransfer(withdrawProxy, amount);
     return amount;
   }

```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L96-L108
### PublicVault.sol.minDepositAmount(): ERC20(asset()).decimals() should be cached rather than call it twice
```solidity
File: /src/PublicVault.sol
96:  function minDepositAmount()
97:    public
98:    view
99:    virtual
100:    override(ERC4626Cloned)
101:    returns (uint256)
102:  {
103:    if (ERC20(asset()).decimals() == uint8(18)) { //@audit: Initial call
104:      return 100 gwei;
105:    } else {
106:      return 10**(ERC20(asset()).decimals() - 1);//@audit: second call
107:    }
108:  }
```

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..c52356e 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -100,10 +100,11 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
     override(ERC4626Cloned)
     returns (uint256)
   {
-    if (ERC20(asset()).decimals() == uint8(18)) {
+    uint8 _assetDecimals = ERC20(asset()).decimals();
+    if (_assetDecimals== uint8(18)) {
       return 100 gwei;
     } else {
-      return 10**(ERC20(asset()).decimals() - 1);
+      return 10**(_assetDecimals - 1);
     }
   }
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L216-L231
### PublicVault.sol.\_deployWithdrawProxyIfNotDeployed(): ROUTER() should be cached
```solidity
File: /src/PublicVault.sol

219:    if (s.epochData[epoch].withdrawProxy == address(0)) {
220:      s.epochData[epoch].withdrawProxy = ClonesWithImmutableArgs.clone(
221:        IAstariaRouter(ROUTER()).BEACON_PROXY_IMPLEMENTATION(),//@audit: 1st call
222:        abi.encodePacked(
223:          address(ROUTER()), // router is the beacon //@audit: 2nd call
```

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..39b7be6 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -217,10 +217,11 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
     internal
   {
     if (s.epochData[epoch].withdrawProxy == address(0)) {
+      IAstariaRouter _router = ROUTER();
       s.epochData[epoch].withdrawProxy = ClonesWithImmutableArgs.clone(
-        IAstariaRouter(ROUTER()).BEACON_PROXY_IMPLEMENTATION(),
+        IAstariaRouter(_router).BEACON_PROXY_IMPLEMENTATION(),
         abi.encodePacked(
-          address(ROUTER()), // router is the beacon
+          address(_router), // router is the beacon
           uint8(IAstariaRouter.ImplementationType.WithdrawProxy),
           asset(), // token
           address(this), // vault
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L322-L326
### PublicVault.sol.processEpoch(): totalAssets() should be cached
```solidity
File: /src/PublicVault.sol
322:        if (totalAssets() > expected) { //@audit: Initial call
323:          s.withdrawReserve = (totalAssets() - expected)//@audit: 2nd call
324:            .mulWadDown(s.liquidationWithdrawRatio)
325:            .safeCastTo88();
326:        } else {
```

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..4f5f6b8 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -317,10 +317,10 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {

       currentWithdrawProxy.setWithdrawRatio(s.liquidationWithdrawRatio);
       uint256 expected = currentWithdrawProxy.getExpected();
-
-      unchecked {
-        if (totalAssets() > expected) {
-          s.withdrawReserve = (totalAssets() - expected)
+      uint256 _totalAssets = totalAssets();
+      unchecked {
+        if (_totalAssets > expected) {
+          s.withdrawReserve = (_totalAssets - expected)
             .mulWadDown(s.liquidationWithdrawRatio)
             .safeCastTo88();
         } else {
@@ -330,7 +330,7 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
       _setYIntercept(
         s,
         s.yIntercept -
-          totalAssets().mulDivDown(s.liquidationWithdrawRatio, 1e18)
+           _totalAssets.mulDivDown(s.liquidationWithdrawRatio, 1e18)
       );
       // burn the tokens of the LPs withdrawing
       _burn(address(this), proxySupply);
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L359-L411
### PublicVault.sol.transferWithdrawReserve(): Result of asset() should be cached
```solidity
File: /src/PublicVault.sol
359:  function transferWithdrawReserve() public {

371:    if (currentWithdrawProxy != address(0)) {
372:      uint256 withdrawBalance = ERC20(asset()).balanceOf(address(this)); //@audit: Initial call

384:      ERC20(asset()).safeTransfer(currentWithdrawProxy, withdrawBalance);//@audit: second call
```


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L597-L609
### PublicVault.sol.\_handleStrategistInterestReward(): VAULT_FEE() should be cached
```solidity
File: /src/PublicVault.sol
602:    if (VAULT_FEE() != uint256(0)) {
603:      uint256 x = (amount > interestOwing) ? interestOwing : amount;
604:      uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
```

```diff
diff --git a/src/PublicVault.sol b/src/PublicVault.sol
index 16247ce..c5ceb07 100644
--- a/src/PublicVault.sol
+++ b/src/PublicVault.sol
@@ -599,9 +599,10 @@ contract PublicVault is VaultImplementation, IPublicVault, ERC4626Cloned {
     uint256 interestOwing,
     uint256 amount
   ) internal virtual {
-    if (VAULT_FEE() != uint256(0)) {
+    uint256 _vault_fee = VAULT_FEE();
+    if (_vault_fee != uint256(0)) {
       uint256 x = (amount > interestOwing) ? interestOwing : amount;
-      uint256 fee = x.mulDivDown(VAULT_FEE(), 10000);
+      uint256 fee = x.mulDivDown(_vault_fee, 10000);

```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L313-L351
### VaultImplementation.sol.buyoutLien(): ROUTER() should be cached
```solidity
File: /src/VaultImplementation.sol
322:    LienToken lienToken = LienToken(address(ROUTER().LIEN_TOKEN())); //@audit: Initial access

334:    ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);//@audit: 2nd access

343:            lien: ROUTER().validateCommitment({ //@audit: 3rd access
```

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..659db03 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -319,7 +319,8 @@ abstract contract VaultImplementation is
     whenNotPaused
     returns (ILienToken.Stack[] memory, ILienToken.Stack memory)
   {
-    LienToken lienToken = LienToken(address(ROUTER().LIEN_TOKEN()));
+    IAstariaRouter _ROUTER = ROUTER();
+    LienToken lienToken = LienToken(address(_ROUTER.LIEN_TOKEN()));

     (uint256 owed, uint256 buyout) = lienToken.getBuyout(stack[position]);

@@ -331,7 +332,7 @@ abstract contract VaultImplementation is

     _validateCommitment(incomingTerms, recipient());

-    ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
+    ERC20(asset()).safeApprove(address(_ROUTER.TRANSFER_PROXY()), buyout);

     return
       lienToken.buyoutLien(
@@ -340,7 +341,7 @@ abstract contract VaultImplementation is
           encumber: ILienToken.LienActionEncumber({
             amount: owed,
             receiver: recipient(),
-            lien: ROUTER().validateCommitment({
+            lien: _ROUTER.validateCommitment({
               commitment: incomingTerms,
               timeToSecondEpochEnd: _timeToSecondEndIfPublic()
             }),
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L326-L334
### VaultImplementation.sol.buyoutLien(): asset() should be cached
```solidity
File: /src/VaultImplementation.sol
326:    if (buyout > ERC20(asset()).balanceOf(address(this))) { //@audit: asset() Initial call

334:    ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);//@audit: asset() 2nd call
```

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..e003f4e 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -322,8 +322,8 @@ abstract contract VaultImplementation is
     LienToken lienToken = LienToken(address(ROUTER().LIEN_TOKEN()));

     (uint256 owed, uint256 buyout) = lienToken.getBuyout(stack[position]);
-
-    if (buyout > ERC20(asset()).balanceOf(address(this))) {
+    ERC20 _asset = ERC20(asset());
+    if (buyout > _asset.balanceOf(address(this))) {
       revert IVaultImplementation.InvalidRequest(
         InvalidRequestReason.INSUFFICIENT_FUNDS
       );
@@ -331,7 +331,7 @@ abstract contract VaultImplementation is

     _validateCommitment(incomingTerms, recipient());

-    ERC20(asset()).safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);
+    _asset.safeApprove(address(ROUTER().TRANSFER_PROXY()), buyout);

     return
       lienToken.buyoutLien(
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L397-L409
### VaultImplementation.sol.\_handleProtocolFee(): ROUTER() should be cached(happy path)
```solidity
File: /src/VaultImplementation.sol
397:  function _handleProtocolFee(uint256 amount) internal returns (uint256) {
398:    address feeTo = ROUTER().feeTo();
399:    bool feeOn = feeTo != address(0);
400:    if (feeOn) {
401:      uint256 fee = ROUTER().getProtocolFee(amount);
```

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..41ea5ee 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -395,10 +395,11 @@ abstract contract VaultImplementation is
   }

   function _handleProtocolFee(uint256 amount) internal returns (uint256) {
-    address feeTo = ROUTER().feeTo();
+    IAstariaRouter _ROUTER = ROUTER();
+    address feeTo = _ROUTER.feeTo();
     bool feeOn = feeTo != address(0);
     if (feeOn) {
-      uint256 fee = ROUTER().getProtocolFee(amount);
+      uint256 fee = _ROUTER.getProtocolFee(amount);

       unchecked {
         amount -= fee;
```

## Internal/Private functions only called once can be inlined to save gas  `Gas saved: 20 * 20= 400`

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:
`Total Instances: 20`

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L425-L431
```solidity
File: /src/CollateralToken.sol
425:  function _generateValidOrderParameters(
426:    CollateralStorage storage s,
427:    address settlementToken,
428:    uint256 collateralId,
429:    uint256[] memory prices,
430:    uint256 maxDuration
431:  ) internal returns (OrderParameters memory orderParameters) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L502-L506
```solidity
File: /src/CollateralToken.sol
502:  function _listUnderlyingOnSeaport(
503:    CollateralStorage storage s,
504:    uint256 collateralId,
505:    Order memory listingOrder
506:  ) internal {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L541-L543
```solidity
File: /src/CollateralToken.sol
541:  function _settleAuction(CollateralStorage storage s, uint256 collateralId)
542:    internal
543:  {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L556
```solidity
File: /src/PublicVault.sol
556:  function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L407-L411
```solidity
File: /src/AstariaRouter.sol
407:  function _sliceUint(bytes memory bs, uint256 start)
408:    internal
409:    pure
410:    returns (uint256 x)
411:  {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L761-L766
```solidity
File: /src/AstariaRouter.sol
761:  function _executeCommitment(
762:    RouterStorage storage s,
763:    IAstariaRouter.Commitment memory c
764:  )
765:    internal
766:    returns (
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L788-L792
```solidity
File: /src/AstariaRouter.sol
788:  function _transferAndDepositAssetIfAble(
789:    RouterStorage storage s,
790:    address tokenContract,
791:    uint256 tokenId
792:  ) internal {
```


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L379-L383
```solidity
File: /src/VaultImplementation.sol
379:  function _requestLienAndIssuePayout(
380:    IAstariaRouter.Commitment calldata c,
381:    address receiver
382:  )
383:    internal
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L397
```solidity
File: /src/VaultImplementation.sol
397:  function _handleProtocolFee(uint256 amount) internal returns (uint256) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L122-L125
```solidity
File: /src/LienToken.sol
122:  function _buyoutLien(
123:    LienStorage storage s,
124:    ILienToken.LienActionBuyout calldata params
125:  ) internal returns (Stack[] memory newStack, Stack memory newLien) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L233-L239
```solidity
File: /src/LienToken.sol
233:  function _replaceStackAtPositionWithNewLien(
234:    LienStorage storage s,
235:    ILienToken.Stack[] calldata stack,
236:    uint256 position,
237:    Stack memory newLien,
238:    uint256 oldLienId
239:  ) internal returns (ILienToken.Stack[] memory newStack) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L292-L298
```solidity
File: /src/LienToken.sol
292:  function _stopLiens(
293:    LienStorage storage s,
294:    uint256 collateralId,
295:    uint256 auctionWindow,
296:    Stack[] calldata stack,
297:    address liquidator
298:  ) internal {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L459-L463
```solidity
File: /src/LienToken.sol
459:  function _appendStack(
460:    LienStorage storage s,
461:    Stack[] memory stack,
462:    Stack memory newSlot
463:  ) internal returns (Stack[] memory newStack) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L512-L518
```solidity
File: /src/LienToken.sol
512:  function _payDebt(
513:    LienStorage storage s,
514:    address token,
515:    uint256 payment,
516:    address payer,
517:    AuctionStack[] memory stack
518:  ) internal returns (uint256 totalSpent) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L623-L630
```solidity
File: /src/LienToken.sol
623:  function _paymentAH(
624:    LienStorage storage s,
625:    address token,
626:    AuctionStack[] memory stack,
627:    uint256 position,
628:    uint256 payment,
629:    address payer
630:  ) internal returns (uint256) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L663-L667
```solidity
File: /src/LienToken.sol
663:  function _makePayment(
664:    LienStorage storage s,
665:    Stack[] calldata stack,
666:    uint256 totalCapitalAvailable
667:  ) internal returns (Stack[] memory newStack) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L715-L718
```solidity
File: /src/LienToken.sol
715:  function _getMaxPotentialDebtForCollateralUpToNPositions(
716:    Stack[] memory stack,
717:    uint256 n
718:  ) internal pure returns (uint256 maxPotentialDebt) {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L775-L779
```solidity
File: /src/LienToken.sol
775:  function _getRemainingInterest(LienStorage storage s, Stack memory stack)
776:    internal
777:    view
778:    returns (uint256)
779:  {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L855-L858
```solidity
File: /src/LienToken.sol
855:  function _removeStackPosition(Stack[] memory stack, uint8 position)
856:    internal
857:    returns (Stack[] memory newStack)
858:  {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L911-L915
```solidity
File: /src/LienToken.sol
911:  function _setPayee(
912:    LienStorage storage s,
913:    uint256 lienId,
914:    address newPayee
915:  ) internal {
```


## Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L562
**Save 85 gas on average**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 166273    | 183609   | 176773 | 215828 |
| After  | 166189    | 183525   | 176689 | 215761 |

```solidity
File:/src/CollateralToken.sol
562:    Asset memory incomingAsset = s.idToUnderlying[collateralId];
```

```diff
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol

-    Asset memory incomingAsset = s.idToUnderlying[collateralId];
+    Asset storage incomingAsset = s.idToUnderlying[collateralId];
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L390
```solidity
File: /src/CollateralToken.sol
390:    Asset memory underlying = _loadCollateralSlot().idToUnderlying[
391:      collateralId
392:    ];

434:    Asset memory underlying = s.idToUnderlying[collateralId];
```

##  require() or revert() statements that check input arguments should be at the top of the function(Also restructured some if's) 
**Fail early and cheaply**

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting alot of gas in a function that may ultimately revert in the unhappy case.

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L106-L124
```solidity
File: /src/ERC721.sol
106:  function transferFrom(
107:    address from,
108:    address to,
109:    uint256 id
110:  ) public virtual override(IERC721) {
111:    ERC721Storage storage s = _loadERC721Slot();

113:    require(from == s._ownerOf[id], "WRONG_FROM");

115:    require(to != address(0), "INVALID_RECIPIENT");
```

```diff
diff --git a/src/ERC721.sol b/src/ERC721.sol
index 232ccb9..a31968e 100644
--- a/src/ERC721.sol
+++ b/src/ERC721.sol
@@ -108,12 +108,13 @@ abstract contract ERC721 is Initializable, IERC721 {
     address to,
     uint256 id
   ) public virtual override(IERC721) {
+
+    require(to != address(0), "INVALID_RECIPIENT");

     ERC721Storage storage s = _loadERC721Slot();

     require(from == s._ownerOf[id], "WRONG_FROM");

-    require(to != address(0), "INVALID_RECIPIENT");
     require(
       msg.sender == from ||
         s.isApprovedForAll[from][msg.sender] ||
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L64-L71
```solidity
File: /src/VaultImplementation.sol
64:  function incrementNonce() external {
65:    VIData storage s = _loadVISlot();
66:    if (msg.sender != owner() && msg.sender != s.delegate) {
67:      revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
68:    }
69:    s.strategistNonce++;
70:    emit NonceUpdated(s.strategistNonce);
71:  }
```

Since we revert on two occassions ie `msg.sender != owner() && msg.sender != s.delegate` which means that both of those conditions need to be true, we could save some gas used in evaluating the line `VIData storage s = _loadVISlot();` if it so happens that `msg.sender` is not equal to `owner()`. Working as it is, even if the first condition fails, we would have already sent some gas evaluating `VIData storage s = _loadVISlot();` Splitting the if's would avoid the unneccessary wastage of gas incase we fail at the check `msg.sender != owner()`

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..ae86f0f 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -62,8 +62,11 @@ abstract contract VaultImplementation is
   }

   function incrementNonce() external {
+    if (msg.sender != owner()){
+     revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
+    }
     VIData storage s = _loadVISlot();
-    if (msg.sender != owner() && msg.sender != s.delegate) {
+    if (msg.sender != s.delegate) {
       revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
     }
     s.strategistNonce++;
```


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L524-L539
```solidity
File: /src/CollateralToken.sol
524: function settleAuction(uint256 collateralId) public {
525:    CollateralStorage storage s = _loadCollateralSlot();
526:    if (
527:      s.collateralIdToAuction[collateralId] == bytes32(0) &&
528:      ERC721(s.idToUnderlying[collateralId].tokenContract).ownerOf(
529:        s.idToUnderlying[collateralId].tokenId
530:      ) !=
531:      s.clearingHouse[collateralId]
532:    ) {
533:      revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
534:    }
535:    require(msg.sender == s.clearingHouse[collateralId]);
```

```diff
diff --git a/src/CollateralToken.sol b/src/CollateralToken.sol
index c82b400..6640cca 100644
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol
@@ -523,6 +523,8 @@ contract CollateralToken is

   function settleAuction(uint256 collateralId) public {
     CollateralStorage storage s = _loadCollateralSlot();
+    require(msg.sender == s.clearingHouse[collateralId]);
     if (
       s.collateralIdToAuction[collateralId] == bytes32(0) &&
       ERC721(s.idToUnderlying[collateralId].tokenContract).ownerOf(
@@ -532,7 +534,6 @@ contract CollateralToken is
     ) {
       revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
     }
-    require(msg.sender == s.clearingHouse[collateralId]);
     _settleAuction(s, collateralId);
     delete s.idToUnderlying[collateralId];
     _burn(collateralId);
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L109-L143
```solidity
File: /src/CollateralToken.sol
109:  function liquidatorNFTClaim(OrderParameters memory params) external {
110:    CollateralStorage storage s = _loadCollateralSlot();

112:    uint256 collateralId = params.offer[0].token.computeId(
113:      params.offer[0].identifierOrCriteria
114:    );
115:    address liquidator = s.LIEN_TOKEN.getAuctionLiquidator(collateralId);
116:    if (
117:      s.collateralIdToAuction[collateralId] == bytes32(0) ||
118:      liquidator == address(0)
119:    ) {
120:      //revert no auction
121:      revert InvalidCollateralState(InvalidCollateralStates.NO_AUCTION);
122:    }
123:    if (
124:      s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))
125:    ) {
126:      //revert auction params dont match
127:      revert InvalidCollateralState(
128:        InvalidCollateralStates.INVALID_AUCTION_PARAMS
129:      );
130:    }

132:    if (block.timestamp < params.endTime) {
133:      //auction hasn't ended yet
134:      revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
135:    }
```

```diff
diff --git a/src/CollateralToken.sol b/src/CollateralToken.sol
index c82b400..46341f3 100644
--- a/src/CollateralToken.sol
+++ b/src/CollateralToken.sol
@@ -107,6 +107,11 @@ contract CollateralToken is
   }

   function liquidatorNFTClaim(OrderParameters memory params) external {
+
+    if (block.timestamp < params.endTime) {
+      //auction hasn't ended yet
+      revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
+    }
     CollateralStorage storage s = _loadCollateralSlot();

     uint256 collateralId = params.offer[0].token.computeId(
@@ -129,10 +134,6 @@ contract CollateralToken is
       );
     }

-    if (block.timestamp < params.endTime) {
-      //auction hasn't ended yet
-      revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
-    }
```

## `keccak256()` should only need to be called on a specific string literal once

It should be saved to an immutable variable, and the variable used instead. If the hash is being used as a part of a function selector, the cast to `bytes4` should also only be done once

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L162-L165
```solidity
File: /src/ERC20-Cloned.sol
162:    keccak256(
163:    "EIP712Domain(string version,uint256 chainId,address verifyingContract)"
164:    ),
165:    keccak256("1"),
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L310
```solidity
File: /src/CollateralToken.sol
310:      ) != keccak256("FlashAction.onFlashAction")
```


## x += y costs more gas than x = x + y for state variables

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L69

```solidity
File: /src/VaultImplementation.sol
69:    s.strategistNonce++;
```

```diff
diff --git a/src/VaultImplementation.sol b/src/VaultImplementation.sol
index b5ff5d7..b28ac53 100644
--- a/src/VaultImplementation.sol
+++ b/src/VaultImplementation.sol
@@ -66,7 +66,7 @@ abstract contract VaultImplementation is
     if (msg.sender != owner() && msg.sender != s.delegate) {
       revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
     }
-    s.strategistNonce++;
+    ++s.strategistNonce;
     emit NonceUpdated(s.strategistNonce);
   }
```

## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L138-L143
```solidity
File: /src/PublicVault.sol

//@audit: uint64 epoch
138:  function redeemFutureEpoch(
139:    uint256 shares,
140:    address receiver,
141:    address owner,
142:    uint64 epoch
143:  ) public virtual returns (uint256 assets) {

//@audit: uint64 epoch
148:  function _redeemFutureEpoch(
149:    VaultData storage s,
150:    uint256 shares,
151:    address receiver,
152:    address owner,
153:    uint64 epoch
154:  ) internal virtual returns (uint256 assets) {

//@audit: uint64 epoch
192:  function getWithdrawProxy(uint64 epoch) public view returns (WithdrawProxy) {

//@audit: uint64 epoch
216:  function _deployWithdrawProxyIfNotDeployed(VaultData storage s, uint64 epoch)

//@audit: uint48 newSlope
529:  function _setSlope(VaultData storage s, uint48 newSlope) internal {

//@audit: uint64 epoch
534:  function decreaseEpochLienCount(uint64 epoch) public onlyLienToken {

//@audit: uint64 epoch
538:  function _decreaseEpochLienCount(VaultData storage s, uint64 epoch) internal {

//@audit: uint64 end
546:  function getLienEpoch(uint64 end) public pure returns (uint64) {

//@audit: uint64 epoch
556:  function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L268-L272
```solidity
File: /src/VaultImplementation.sol

//@audit: uint40 end
268:  function _afterCommitToLien(
269:    uint40 end,
270:    uint256 lienId,
271:    uint256 slope
272:  ) internal virtual {}

//@audit: uint8 position
313:  function buyoutLien(
314:    ILienToken.Stack[] calldata stack,
315:    uint8 position,
316:    IAstariaRouter.Commitment calldata incomingTerms
317:  )
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L790-L796
```solidity
File: /src/LienToken.sol

//@audit: uint8 position
790:  function _payment(
791:    LienStorage storage s,
792:    Stack[] memory activeStack,
793:    uint8 position,
794:    uint256 amount,
795:    address payer
796:  ) internal returns (Stack[] memory, uint256) {
```

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L710
```solidity
File:/src/PublicVault.sol
710:    return epochEnd - block.timestamp;
```

The operation `epochEnd - block.timestamp` cannot underflow due to the check on [Line 706](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L706) that ensures that `epochEnd` is greater than `block.timestamp` before performing the operation


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L638
```solidity
File: /src/LienToken.sol
638:      remaining = owing - payment;
```

The operation `owing - payment` cannot underflow due to the check on [Line 637](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L637) that ensures that `owing` is greater than `payment` before performing our arithmetic operation

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L830
```solidity
File: /src/LienToken.sol
830:      stack.point.amount -= amount.safeCastTo88();
```

The operation `stack.point.amount - amount` cannot underflow due to the check on [Line 829](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L829) that ensures that `stack.point.amount ` is greater than `amount` before performing the subtraction

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L260
```solidity
File: /src/WithdrawProxy.sol
260:        (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
```
The operation `s.expected - balance` cannot underflow due to the check on [Line 258](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L258) that ensures that `s.expected` is greater than `balance` before performing the subtraction

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L264
```solidity
File: /src/WithdrawProxy.sol
264:     (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
```
The operation `balance - s.expected` cannot underflow as it would only be evaluated if  `balance` is greater than `s.expected`, which is enforced by the check on  [Line 258](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L258)


## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).
The Gas saved could be higher as evident from the first instance due to refactoring which condition is checked first.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65
**Gas benchmarks**
|        | Min    | Average | Median   | Max   |
| ------ | --- | ------- | ----- | ----- |
| Before | 2737    | 15363   | 21677 | 21677 |
| After  | 599    | 15034   | 21677 | 21677 |

```solidity
File: /src/Vault.sol
65:    require(s.allowList[msg.sender] && receiver == owner());
```

```diff
diff --git a/src/Vault.sol b/src/Vault.sol
index cee62cc..140a25f 100644
--- a/src/Vault.sol
+++ b/src/Vault.sol
@@ -61,8 +61,10 @@ contract Vault is VaultImplementation {
     virtual
     returns (uint256)
   {
+       require(receiver == owner());
     VIData storage s = _loadVISlot();
-    require(s.allowList[msg.sender] && receiver == owner());
+    require(s.allowList[msg.sender]);
+
     ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
     return amount;
   }
```

**Other instances:**
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L672-L675
```solidity
File: /src/PublicVault.sol
672:    require(
673:      currentEpoch != 0 &&
674:        msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
675:    );

687:    require(
688:      currentEpoch != 0 &&
689:        msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:    );
```

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L143-L146
```solidity
File: /src/ERC20-Cloned.sol
143:      require(
144:        recoveredAddress != address(0) && recoveredAddress == owner,
145:        "INVALID_SIGNER"
146:      );
```


## Duplicated require()/revert() checks should be refactored to a modifier or function. see [docs](https://solidity.readthedocs.io/en/develop/contracts.html#function-modifiers) 
This saves deployement gas
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L78
```solidity
File: /src/VaultImplementation.sol
78:    require(msg.sender == owner()); //owner is "strategist"
```

The above check has been repeated in the following lines

```solidity
96:    require(msg.sender == owner()); //owner is "strategist"

105:    require(msg.sender == owner()); //owner is "strategist"

114:    require(msg.sender == owner()); //owner is "strategist"

147:    require(msg.sender == owner()); //owner is "strategist"

211:    require(msg.sender == owner()); //owner is "strategist"

```

we can replace the above by using the following modifer

```solidity
modifier onlyOwner(){
	require(msg.sender == owner()); //owner is "strategist"
	_;
}
```

## A modifier used only once and not being inherited should be inlined to save gas

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L253-L263
```solidity
File: /src/CollateralToken.sol
253:  modifier releaseCheck(uint256 collateralId) {
254:    CollateralStorage storage s = _loadCollateralSlot();

255:    if (s.LIEN_TOKEN.getCollateralState(collateralId) != bytes32(0)) {
256:      revert InvalidCollateralState(InvalidCollateralStates.ACTIVE_LIENS);
257:    }
258:    if (s.collateralIdToAuction[collateralId] != bytes32(0)) {
259:      revert InvalidCollateralState(InvalidCollateralStates.AUCTION_ACTIVE);
260:    }
261:    _;
262:  }
```

The above modifier is only being called on [Line 333](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L333)

