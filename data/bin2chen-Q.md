Low:
1.deposit() check wrong variant

```solidity
  function deposit(uint256 assets, address receiver)
    public
    virtual
    returns (uint256 shares)
  {

-    require(shares > minDepositAmount(), "VALUE_TOO_SMALL");
+    require(assets > minDepositAmount(), "VALUE_TOO_SMALL");
```

2.minDepositAmount() When the asset is btc, the minDepositAmount is too large

when asset == btc , minDepositAmount = 0.1 btc , equal 2000 usd
suggest:
```solidity
  function minDepositAmount()
    public
    view
    virtual
    override(ERC4626Cloned)
    returns (uint256)
  {
    if (ERC20(asset()).decimals() == uint8(18)) {
      return 100 gwei;
    } else {
-     return 10**(ERC20(asset()).decimals() - 1);
+     return 10**(ERC20(asset()).decimals() /2);
    }
  }
```