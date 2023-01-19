# Report
## Gas Optimizations ##
### [G-1]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```(s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)``` [L260](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L260) (s.expected - balance)
1. ```return epochEnd - block.timestamp;``` [L710](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L710) 
1. ```uint256 j = i - 1;``` [L154](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L154) 
1. ```uint256 j = i - 1;``` [L473](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L473) 
1. ```remaining = owing - payment;``` [L638](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L638)
1. ```stack.point.amount -= amount.safeCastTo88();``` [L830](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L830)

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
