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

G3. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L240-L266
Cache function call results:
```
ERC20 asset = asset();
PublicVault vault = VAULT();

```

G4. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L275
Caching ``s.currentEpoch`` can save gas.

G5. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L323-L325
Adding unchecked can save gas since we already know underflow is impossible due to the condition of the if statement
```
unchecked{
     s.withdrawReserve = (totalAssets() - expected)
            .mulWadDown(s.liquidationWithdrawRatio)
            .safeCastTo88();
}

```
