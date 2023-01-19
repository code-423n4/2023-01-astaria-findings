# Gas Optimizations Report for Astaria contest
## Overview
During the audit, 2 gas issues were found.  

№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use unchecked blocks for subtractions where underflow is impossible | 7 | 245
G-2 | Using ```storage``` pointer to ```struct``` is cheaper than using ```memory``` | 4 | 

## Gas Optimizations Findings(2)
### G-1. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L260 (s.expected - balance)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L264 (balance - s.expected)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L710 (epochEnd - block.timestamp)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L154 (i - 1)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L473 (i - 1)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L638 (owing - payment)
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L830 (stack.point.amount -= amount.safeCastTo88())

##### Saved
This saves ~35.  
So, ~35*7 = 245
#
### G-2. Using ```storage``` pointer to ```struct``` is cheaper than using ```memory```
##### Description
When you copy a storage ```struct```/```array```/```mapping``` to a memory variable, you are copying each member by reading it from the storage, which is expensive, but when you use the ```storage``` keyword, you are just storing a pointer to the storage, which is much cheaper.
##### Instances
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L137 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L341
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L434
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L562

##### Recommendation
For example, change: 
```
Asset memory underlying = s.idToUnderlying[collateralId];
```  
to:  
```
Asset storage underlying = s.idToUnderlying[collateralId];
```