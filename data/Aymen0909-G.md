# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1 | `storage` variable should be cached into `memory` variables instead of re-reading them  |  5 |
| 2 | Use `unchecked` blocks to save gas  |  2 |
| 3 | Remove redundant checks |  1 |
| 4 | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  8 |
| 5 | Splitting `require()` statements that uses && saves gas  |  3 |


## Findings

### 1- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 5 instances of this issue :

File: src/WithdrawProxy.sol [Line 247-286](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L247-L286)

In the code linked above the function `VAULT()` is called multiple times, this function reads from storage the address of the Vault and because this address won't be changed in those lines of code the function `VAULT()` should be called at the beginning and the Vault address should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: src/WithdrawProxy.sol [Line 258-271](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L258-L271)

In the code linked above the values of `s.withdrawRatio` and `s.expected` are read multiple times (more than 3) from storage and their respective values does not change so they should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: src/CollateralToken.sol [Line 299-325](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L299-L325)

In the code linked above the value of `s.clearingHouse[collateralId]` is read multiple times (3) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: src/CollateralToken.sol [Line 451-463](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L451-L463)

In the code linked above the value of `s.clearingHouse[collateralId]` is read multiple times (4) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: src/CollateralToken.sol [Line 531-535](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L531-L535)

In the code linked above the value of `s.clearingHouse[collateralId]` is read multiple times (2) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.


### 2- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 2 instances of this issue:

File: src/LienToken.sol [Line 638](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L638)
```
remaining = owing - payment;
```

The above operation cannot underflow due to the check :

File: src/LienToken.sol [Line 637](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L637)
```
if (owing > payment.safeCastTo88())
```

So the operation should be marked as `unchecked`. 

File: src/LienToken.sol [Line 830](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L830)
```
stack.point.amount -= amount.safeCastTo88();
```

The above operation cannot underflow due to the check :

File: src/LienToken.sol [Line 829](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L829)
```
if (stack.point.amount > amount)
```

So the operation should be marked as `unchecked`. 


### 3- Remove redundant checks :

In the function below there are two collateral owner checks (modifier + if check), this is redundant and should be removed to save gas.

File: src/CollateralToken.sol [Line 331-346](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L331-L346)
```
function releaseToAddress(uint256 collateralId, address releaseTo)
public
releaseCheck(collateralId)
onlyOwner(collateralId)
{
    CollateralStorage storage s = _loadCollateralSlot();

    if (msg.sender != ownerOf(collateralId)) {
        revert InvalidSender();
    }
    Asset memory underlying = s.idToUnderlying[collateralId];
    address tokenContract = underlying.tokenContract;
    _burn(collateralId);
    delete s.idToUnderlying[collateralId];
    _releaseToAddress(s, underlying, collateralId, releaseTo);
}
```

### 4- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 8 instances of this issue:

```
File: src/WithdrawProxy.sol

237     s.withdrawReserveReceived += amount;
313     s.expected += newLienExpectedValue.safeCastTo88();

File: src/PublicVault.sol

380     s.withdrawReserve -= withdrawBalance.safeCastTo88();
405     s.withdrawReserve -= drainBalance.safeCastTo88();
565     s.slope += computedSlope.safeCastTo48();
583     s.yIntercept += assets.safeCastTo88();
606     s.strategistUnclaimedShares += feeInShares;
624     s.yIntercept += params.increaseYIntercept.safeCastTo88();
```

### 5- Splitting `require()` statements that uses && saves gas :

There are 3 instances of this issue :

```
File: src/Vault.sol

65      require(s.allowList[msg.sender] && receiver == owner());

File: src/LienToken.sol

672     require(
            currentEpoch != 0 &&
            msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
        );
687     require(
            currentEpoch != 0 &&
            msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
        );
```