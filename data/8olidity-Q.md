## parameter not used


Both the `id` parameter in `balanceof()` and the `ids` parameter in `balanceofBatch()` are not used

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L102



```solidity
  function balanceOf(address account, uint256 id) //@audit  
    external
    view
    returns (uint256)
  {
    return type(uint256).max;
  }

  function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)//@audit  
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
```