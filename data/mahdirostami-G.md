# 1.Use function instead of modifiers

```diff
@@ -125,14 +125,8 @@ contract AstariaRouter is
     address to,
     uint256 shares,
     uint256 maxAmountIn
-  )
-    public
-    payable
-    virtual
-    override
-    validVault(address(vault))
-    returns (uint256 amountIn)
-  {
+  ) public payable virtual override returns (uint256 amountIn) {
+    validVault(address(vault));
     return super.mint(vault, to, shares, maxAmountIn);
   }
```
```diff
-  modifier validVault(address targetVault) {
+  //USE FUNCTION INSTEAD OF MODIFIERS
+  function validVault(address targetVault) private view {
     if (!isValidVault(targetVault)) {
       revert InvalidVault(targetVault);
     }
-    _;
   }
```
Affected code:
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L133
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L149
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L181
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L192
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L583

# 2.<x> += <y> costs more gas than <x> = <x> + <y> for state variables

```diff
-      totalBorrowed += payout;
+      totalBorrowed = totalBorrowed + payout;
```
Affected code:
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L512
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L210
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L679
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L720
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L735
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L237

# 3.Ternary operation is cheaper than if-else statement

```diff
-    if (epochLength > uint256(0)) {
-      vaultType = uint8(ImplementationType.PublicVault);
-    } else {
-      vaultType = uint8(ImplementationType.PrivateVault);
-    }
+    epochLength > uint256(0)
+      ? vaultType = uint8(ImplementationType.PublicVault)
+      : vaultType = uint8(ImplementationType.PrivateVault);
```

Affected code:
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L724

# 4. STATE VARIABLES CAN BE PACKED INTO Less STORAGE SLOTS.

```diff
     uint256 owed = _getOwed(stack, block.timestamp);
     address lienOwner = ownerOf(lienId);
+    address payee = _getPayee(s, lienId);
     bool isPublicVault = _isPublicVault(s, lienOwner);
-    address payee = _getPayee(s, lienId);
);
```

Affected code:
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L808

