## Unneeded functions
In ClearingHouse.sol, the function parameters of `balanceOf()` and `balanceOfBatch()` aren't utilized in the respective function logic to retrieve any state variables. The code blocks entailed are essentially associated with a pure function logic. Since these external functions are neither called internally nor inheritable in the child contracts, consider removing them to save gas on contract deployment. If need be, `type(uint256).max` can always be coded inline by the calling contracts/functions instead of being externally interacted with.

[File: ClearingHouse.sol#L90-L112](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-L112)

```solidity
  function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
    external
    view
    returns (uint256[] memory output)
  {
    output = new uint256[](accounts.length);
    for (uint256 i; i < accounts.length; ) {
      output[i] = type(uint256).max;
      unchecked {
        ++i;
      }
    }
  }

  function setApprovalForAll(address operator, bool approved) external {}

  function isApprovedForAll(address account, address operator)
    external
    view
    returns (bool)
  {
    return true;
  }
```
## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here are some of the instances entailed:

[File: Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

```solidity
    require(s.allowList[msg.sender] && receiver == owner());
```
## Unneeded function parameter
In `deposit()` of Vault.sol, the input parameter of `receiver` is only used for comparing with `owner()` and no state change is associated with it. Consider having the code block refactored as follows to save gas both on contract deployment and function calls:

[File: Vault.sol#L59-L68](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L59-L68)

```diff
-  function deposit(uint256 amount, address receiver)
+  function deposit(uint256 amount)
    public
    virtual
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
-    require(s.allowList[msg.sender] && receiver == owner());
+    require(s.allowList[msg.sender] || msg.sender == owner());
    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
    return amount;
  }
```
Note: The second condition in the require statement may be omitted to save even more gas if the owner gets himself enabled via 
[`modifyAllowList()`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L95-L99).

## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the code line instance below may be refactored as follows:

[File: VaultImplementation.sol#L66](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L66)

```diff
-    if (msg.sender != owner() && msg.sender != s.delegate) {
+    if (!(msg.sender == owner() || msg.sender == s.delegate)) {
```
## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: VaultImplementation.sol#L131-L140](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L131-L140)

```diff
+    function _whenNotPaused() private view {
+        if (ROUTER().paused()) {
+          revert InvalidRequest(InvalidRequestReason.PAUSED);
+        }
+
+        if (_loadVISlot().isShutdown) {
+          revert InvalidRequest(InvalidRequestReason.SHUTDOWN);
+        }
+    }

    modifier whenNotPaused() {
-        if (ROUTER().paused()) {
-          revert InvalidRequest(InvalidRequestReason.PAUSED);
-        }
-
-        if (_loadVISlot().isShutdown) {
-          revert InvalidRequest(InvalidRequestReason.SHUTDOWN);
-        }
+        _whenNotPaused();
        _;
    }
```