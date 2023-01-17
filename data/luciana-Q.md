 ## [Info]  `address(0)` can be enable as depositor

The `modifyAllowList` function does not validate the `depositor` address. Despite, this function can be called only by the owner,  the `allowList`  could set to `address(0)` as mistake.

[`VaultImplementation.sol#L95`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L95)

```solidity
function modifyAllowList(address depositor, bool enabled) external virtual {
    require(msg.sender == owner()); //owner is "strategist"
    _loadVISlot().allowList[depositor] = enabled;
    emit AllowListUpdated(depositor, enabled);
  }
```
*Recommendation: * The function should check if the `depositor` local variable is a valid address.