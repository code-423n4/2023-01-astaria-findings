**Vulnerability details:**

**Impact:**
Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever.

**Proof of Concept:**
missing zero address check in src/TransferProxy.sol
```
  function tokenTransferFrom(
    address token,
    address from,
    address to,
    uint256 amount
  ) external requiresAuth {
    ERC20(token).safeTransferFrom(from, to, amount);
  }
```
**Tools Used:**
Manual Analysis

**Recommended Mitigation Steps:**
Add zero-address checks
