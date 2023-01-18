# In AstariaRouter.sol, function newVault() has no zero address check.  invalid address can become new vault. 
```
  function newVault(address delegate, address underlying)
    external
    whenNotPaused
    returns (address)
  {
    address[] memory allowList = new address[](1);
    allowList[0] = msg.sender;
    RouterStorage storage s = _loadRouterSlot();


    return
      _newVault(
        s,
        underlying,
        uint256(0),
        delegate,
        uint256(0),
        true,
        allowList,
        uint256(0)
      );
  }
```
# public function drain() in withdrawProxy.sol has no zero-address checker. funds can be drained to an invalid address.
```
  function drain(uint256 amount, address withdrawProxy)
    public
    onlyVault
    returns (uint256)
  {
    uint256 balance = ERC20(asset()).balanceOf(address(this));
    if (amount > balance) {
      amount = balance;
    }
    ERC20(asset()).safeTransfer(withdrawProxy, amount);
    return amount;
  }
```

## unnamed and unused parameter in function _execute(). "uint256 // space to encode whatever is needed",
```
  function _execute(
    address tokenContract, // collateral token sending the fake nft
    address to, // buyer
    uint256 encodedMetaData, //retrieve token address from the encoded data
   uint256 // space to encode whatever is needed,
  ) internal { }
```
