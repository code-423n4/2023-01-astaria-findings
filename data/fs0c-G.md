## [G-01] **Can stop loop early in _payDebt when everything is spent.**

When a loan is sold on Seapost and `_payDebt` is called, it loops thought the auction stack and calls `_paymentAH` for each, decrementing the remaining payment as money is spent.

This loop can be ended when payment == 0.

### Recommendation

```diff
uint256 i;
     for (; i < stack.length;) {
+      if (payment == 0) break;
       uint256 spent;
       unchecked {
         spent = _paymentAH(s, token, stack, i, payment, payer);
```

## [G-02] Remove unused variable.

1. The local variable in function `LientToken.sol:_paymentAH`  can be removed as it is not used.

```diff
uint256 end = stack[position].end;
```

1. The local variable in function `CollateralToken.sol:releaseToAddress`  can be removed as it is not used

```solidity
    address tokenContract = underlying.tokenContract;
```

## [G-03] Unnecessary use of mulDivDown.

In PublicVault.sol 

```solidity
function _totalAssets(VaultData storage s) internal view returns (uint256) {
    uint256 delta_t = block.timestamp - s.last;
    return uint256(s.slope).mulDivDown(delta_t, 1) + uint256(s.yIntercept);
  }
```

`uint256(s.slope).mulDivDown(delta_t, 1)` can be change to `uint256(s.slope)*delta_t` to save gas.