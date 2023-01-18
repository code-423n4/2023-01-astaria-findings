# Use a modifier or an auxiliary function

```diff
+ error OnlyOwner();

+ function _onlyOwner() private view {
+   if (msg.sender != owner()) {
+     revert OnlyOwner();
+   }
+ }

  /**
   * @notice modify the deposit cap for the vault
   * @param newCap The deposit cap.
   */
  function modifyDepositCap(uint256 newCap) external {
-   require(msg.sender == owner()); //owner is "strategist"
+   _onlyOwner();
    _loadVISlot().depositCap = newCap.safeCastTo88();
  }

  /**
   * @notice disable the allowList for the vault
   */
  function disableAllowList() external virtual {
-   require(msg.sender == owner()); //owner is "strategist"
+   _onlyOwner();
    _loadVISlot().allowListEnabled = false;
    emit AllowListEnabled(false);
  }

  /**
   * @notice enable the allowList for the vault
   */
  function enableAllowList() external virtual {
-   require(msg.sender == owner()); //owner is "strategist"
+   _onlyOwner();
    _loadVISlot().allowListEnabled = true;
    emit AllowListEnabled(true);
  }

  function shutdown() external {
-   require(msg.sender == owner()); //owner is "strategist"
+   _onlyOwner();
    _loadVISlot().isShutdown = true;
    emit VaultShutdown();
  }


  function setDelegate(address delegate_) external {
-   require(msg.sender == owner()); //owner is "strategist"
+   _onlyOwner();
    VIData storage s = _loadVISlot();
    s.delegate = delegate_;
    emit DelegateUpdated(delegate_);
    emit AllowListUpdated(delegate_, true);
  }
```

# Use `calldata` instead of `memory` for function input params
* https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/CollateralToken.sol#L109

