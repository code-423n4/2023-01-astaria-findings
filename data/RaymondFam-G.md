## Unneeded functions
In ClearingHouse.sol, the function parameters of `balanceOf()` and `balanceOfBatch()` aren't utilized in the respective function logic to retrieve any state variables. The code blocks entailed are essentially associated with a pure function logic. These external functions are neither called internally nor inherited, and additionally, there are no `override` and/or `virtual` visibility associated.

Consider removing them to save gas on contract deployment. If need be, `type(uint256).max` can always be coded inline by the calling contracts/functions instead of being externally interacted with.

[File: ClearingHouse.sol#L82-L102](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L102)

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
## Functions of similar logic can be merged
`disableAllowList()` and `enableAllowList()` bear similar (if not identical) logic. 

Consider having them merged as follows to save gas on contract deployment:

[File: VaultImplementation.sol#L104-L117](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L104-L117)

```solidity
  function toggleAllowList(bool enabled) external virtual {
    require(msg.sender == owner()); //owner is "strategist"
    _loadVISlot().allowListEnabled = enabled;
    emit AllowListEnabled(enabled);
  }
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `-=` instance below may be refactored as follows:

[File: VaultImplementation.sol#L404](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L404)

```diff
-        amount -= fee;
+        amount = amount - fee;
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following inequality instances may be refactored as follows:

[File: AstariaRouter.sol#L697-L702](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L697-L702)

```diff
-      (newLien.details.rate <= maxNewRate &&
+      (newLien.details.rate < maxNewRate + 1 &&
-        newLien.details.duration + block.timestamp >=
+        newLien.details.duration + block.timestamp >
-        stack[position].point.end) ||
+        stack[position].point.end - 1) ||
-      (block.timestamp + newLien.details.duration - stack[position].point.end >=
+      (block.timestamp + newLien.details.duration - stack[position].point.end >
-        s.minDurationIncrease &&
+        s.minDurationIncrease - 1 &&
-        newLien.details.rate <= stack[position].lien.details.rate);
+        newLien.details.rate < stack[position].lien.details.rate + 1);
```
## Unneeded check
In `deposit()` of ERC4626-Cloned.sol, `shares > minDepositAmount()` signifies that `shares != 0`. As such, the first require statement is just a waste of gas.

Consider having the function refactored as follows:

[File: ERC4626-Cloned.sol#L19-L36](https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L19-L36)

```diff
  function deposit(uint256 assets, address receiver)
    public
    virtual
    returns (uint256 shares)
  {
-    // Check for rounding error since we round down in previewDeposit.
-    require((shares = previewDeposit(assets)) != 0, "ZERO_SHARES");

-    require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
+    require((shares = previewDeposit(assets)) > minDepositAmount(), "VALUE_TOO_SMALL");

    ...
```
## Unneeded code line 
In `deposit()` of PublicVault.sol, `totalAssets()` is cached but never used in the function call. 

Consider removing this unused line of code to save gas both on contract deployment and function calls:

```diff
  function deposit(uint256 amount, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[receiver]);
    }

-    uint256 assets = totalAssets();

    return super.deposit(amount, receiver);
  }
```
## Unneeded `unit256()` cast
Casting the numeral `0` to `uint256()` in the following instances is unnecessary and a waste of gas since `0` is already an unsigned integer belonging to any size, i.e. uint8, uint16, ..., uint256.

[File: AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol)

```solidity
458:    if (details.rate == uint256(0) || details.rate > s.maxInterestRate) {

535:        uint256(0),

537:        uint256(0),

540:        uint256(0),

724:    if (epochLength > uint256(0)) {
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ... }` to use the previous wrapping behavior further saves gas.

For instance, the SafeMath instance in the for loop below could be extended to include `+=` too:

[File: AstariaRouter.sol#L505-L515](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L505-L515)

```diff
    uint256 i;
    for (; i < commitments.length; ) {
      if (i != 0) {
        commitments[i].lienRequest.stack = stack;
      }
      uint256 payout;
      (lienIds[i], stack, payout) = _executeCommitment(s, commitments[i]);
+      unchecked {
      totalBorrowed += payout;
-      unchecked {
        ++i;
      }
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.17",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: AstariaRouter.sol#L402-L405](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L402-L405)

```diff
-  function getAuctionWindow(bool includeBuffer) public view returns (uint256) {
+  function getAuctionWindow(bool includeBuffer) public view returns (uint256 auctionWindow) {
    RouterStorage storage s = _loadRouterSlot();
-    return s.auctionWindow + (includeBuffer ? s.auctionWindowBuffer : 0);
+    auctionWindow = s.auctionWindow + (includeBuffer ? s.auctionWindowBuffer : 0);
  }
```
## Division by 1
Using `mulDivDown` from FixedPointMathLib.sol to divide x * y by 1, the denominator, is a waste of gas.

Consider having the following instance refactored as follows:

[File: PublicVault.sol#L490-L493](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L490-L493)

```diff
  function _totalAssets(VaultData storage s) internal view returns (uint256) {
    uint256 delta_t = block.timestamp - s.last;
-    return uint256(s.slope).mulDivDown(delta_t, 1) + uint256(s.yIntercept);
+    return uint256(s.slope) * delta_t + uint256(s.yIntercept);
  }
```
## Inline over internal
Internal function entailing only one line of code may be embedded in the calling function(s) to save gas.

Here is an instance entailed:

[File: PublicVault.sol#L556-L560](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L556-L560)

```solidity
  function _increaseOpenLiens(VaultData storage s, uint64 epoch) internal {
    unchecked {
      s.epochData[epoch].liensOpenForEpoch++;
    }
  }
```