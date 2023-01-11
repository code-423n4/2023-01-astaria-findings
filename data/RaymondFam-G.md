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
In `deposit()` of Vault.sol, the input parameter of `receiver` is only used for comparing with `owner()` and no state change is associated with it. Additionally, with the owner the only depositor allowed when having a new vault deployed via [`newVault()`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L527-L528) in AstariaRouter.sol, the second condition in the require statement may be omitted to save even more gas on contract deployment and function calls.

Consider having the code block refactored as follows :

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
+    require(s.allowList[msg.sender];
    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
    return amount;
  }
```
## `newVault() and its associated logic could be trimmed
In `newVault()` of AstariaRouter.sol, since `owner()` is the only depositor allowed in `allowList`, it does not make much sense caching a 1 element array to update the mapping `allowlist`. 

Consider having the unnecessary code lines removed to save gas both on contract deployment and function calls:

[AstariaRouter.sol#L522-L542](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L522-L542) 

```diff
  function newVault(address delegate, address underlying)
    external
    whenNotPaused
    returns (address)
  {
-    address[] memory allowList = new address[](1);
-    allowList[0] = msg.sender;
    RouterStorage storage s = _loadRouterSlot();

    return
      _newVault(
        s,
        underlying,
        uint256(0),
        delegate,
        uint256(0),
-        true,
+        false,
-        allowList,
+        /* emptyList */,
        uint256(0)
      );
  }
```
With the above trim measure in place, the second if block along with its nested for loop in the `init()` instance of VaultImplementation.sol will be skipped when externally invoked via `IVaultImplementation(vaultAddr).init(IVaultImplementation.InitParams()`, saving even more gas at deployment:

[File: VaultImplementation.sol#L190-L208](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L190-L208)

```solidity
  function init(InitParams calldata params) external virtual {
    require(msg.sender == address(ROUTER()));
    VIData storage s = _loadVISlot();

    if (params.delegate != address(0)) {
      s.delegate = params.delegate;
    }
    s.depositCap = params.depositCap.safeCastTo88();
    if (params.allowListEnabled) {
      s.allowListEnabled = true;
      uint256 i;
      for (; i < params.allowList.length; ) {
        s.allowList[params.allowList[i]] = true;
        unchecked {
          ++i;
        }
      }
    }
  }
```
Lastly, `deposit()` in Vault.sol will need to be refactored as follows to accommodate the trimming changes above:

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
+    require(msg.sender == owner());
    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
    return amount;
  }
```
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
## Unneeded event emission
`setDelegate()` has the event `AllowListUpdated()` emitted without having `delegate_` enabled in the mapping `allowList`.

Consider removing this irrelevant emission to save gas both on contract deployment and function calls:

[File: VaultImplementation.sol#L210-L216](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L210-L216)

```diff
  function setDelegate(address delegate_) external {
    require(msg.sender == owner()); //owner is "strategist"
    VIData storage s = _loadVISlot();
    s.delegate = delegate_;
    emit DelegateUpdated(delegate_);
-    emit AllowListUpdated(delegate_, true);
  }
```
