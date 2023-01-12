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

