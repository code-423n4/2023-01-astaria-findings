In ClearingHouse.sol - https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-#L102
It seems that the function `balanceOfBatch()` return the maximum value of a uint256 for every element in the output array. 

To fix it, you can modify the `balanceOfBatch` function to return the correct balance of the specified accounts and ids. Here is a example/reference:
```
for (uint256 i; i < accounts.length; ) {
      output[i] = balanceOf(accounts[i], ids[i]);
      unchecked {
        ++i;
}
```