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

The local variable in function `LientToken.sol:_paymentAH`  can be removed as it is not used.

```diff
uint256 end = stack[position].end;
```