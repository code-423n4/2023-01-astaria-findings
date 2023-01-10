G1. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L153-L175
Caching ``params.encumber.stack[j]`` can save gas: 
```
Stack stack = params.encumber.stack[j]; 

```