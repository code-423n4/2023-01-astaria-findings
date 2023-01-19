# Astaria

## Gas Optimizations Report

### G-01 Splitting `require()` statements that use `&&` saves gas

Instead of using `&&` on single require check using two require checks can save gas

_There are **2** instances of this issue:_

```solidity
673: currentEpoch != 0 &&

688: currentEpoch != 0 &&
```

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol
