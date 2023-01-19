G1. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L153-L175
Caching ``params.encumber.stack[j]`` can save gas: 
```
Stack stack = params.encumber.stack[j]; 

```

G2. https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L148
Introducing a storage variable to store ``s.epochData[epoch].withdrawProxy`` can save gas:
```
WithdrawProxy storage wproxy = s.epochData[epoch].withdrawProxy;

```

G3. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L262
Deleting this line can save gas since this line is not needed.


G4. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L432-L433
Dropping the second condition since this first condition already implies the second one.

G5. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L684
Caching ``newLien.details.rate`` and ``newLien.details.duration`` can save gas

G6. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L138-L142
This check is not necessary and can be eliminated to save gas sine this check has already been conducted inside the previous call `` _createLien(s, params.encumber)``. 

G7. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L397-L399
Deleting this line can save gas since we already make such assignment at L366.

G8. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L400-L403
We can use variable ``currentWithdrawProxy`` to save gas here:
```
uint256 drainBalance = WithdrawProxy(withdrawProxy).drain(
        s.withdrawReserve,
        currentWithdrawProxy
      );
```
G9. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L855-L881
Much gas can be saved with the following implementation since we have less memory copies. 
```
function _removeStackPosition(Stack[] memory stack, uint8 position)
    internal
    returns (Stack[] memory)
  {
    require(position < length);

    for (int i=position; i < length - 1; ) {
      unchecked {
        stack[i] = stack[i + 1];
        ++i;
      }

      stack.pop();
    }
     
     emit LienStackUpdated(
      stack[position].lien.collateralId,
      position,
      StackAction.REMOVE,
      uint8(newStack.length)
    );

    return stack;

  }


```
G10. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L439-L457
_afterCommitToLien() can be deleted since it is not used anywhere. 

G11. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L258-L266
Enclosing this block inside ``unchecked`` can save gas since it is impossible to have underflow/overflow here.
```
unchecked{
if (balance < s.expected) {
      PublicVault(VAULT()).decreaseYIntercept(
        (s.expected - balance).mulWadDown(1e18 - s.withdrawRatio)
      );
    } else {
      PublicVault(VAULT()).increaseYIntercept(
        (balance - s.expected).mulWadDown(1e18 - s.withdrawRatio)
      );
    }
}
```

G12. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L56-L85
Introducing a constant (such 10,000) as the denomiator for all denominator paramters can save gas. 

G13. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L490-L493
Using ``*`` instead of ``muldivdown`` can save gas here.
```
function _totalAssets(VaultData storage s) internal view returns (uint256) {
    uint256 delta_t = block.timestamp - s.last;
    return uint256(s.slope*delta_t) + uint256(s.yIntercept);
  }

```

G14. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L338-L340
This if-block check can be deleted since the modifier ``onlyOwner(collateralId)`` has done the job.

G15. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L342
This line can be deleted since ``tokenContract`` is not used at all.

G16. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L138-L139
These two lines can be deleted since they are not used afterwards. 

G17. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L154
Enclosing it inside unchecked can save gas since underflow is impossible due to the check in the for loop.
In addition, j can be defined outside. 
```
uint256 j = i - 1;

```

G18. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L122
Caching ``params.encumber.stack[params.position]`` can save gas for function ``_buyoutLien()``:
```
Stack calldata currentStack = params.encumber.stack[params.position];

```

G19. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L107
Caching ``params.encumber.lien.collateralId`` can save gas. 
```
uint256 collateralId = params.encumber.lien.collateralId; 

```

G20. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L309
Introducing ``owed`` outside of the for loop can save gas to avoid allocating a new variable for each iteration.

G21. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L389
Caching ``params.lien.collateralId`` can save gas.
```
uint256 collateralId = params.lien.collateralId;
```

G22. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L464
Caching ``stack.length`` can save gas.
```
uint oldStackLength = stack.length;
```
G23:  https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L649-L653
This is only necessary when ``remaining != 0``, so we can add this condition to short-circuit to save gas:
```
if (remaining != 0 && _isPublicVault(s, payee)) {  // @audit: use short-circuit here by adding condition 1
      IPublicVault(payee).updateAfterLiquidationPayment(
        IPublicVault.LiquidationPaymentParams({remaining: remaining})
      );
}
```


