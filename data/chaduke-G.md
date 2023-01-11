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

G3. 

