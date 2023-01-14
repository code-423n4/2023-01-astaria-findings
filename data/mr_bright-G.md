**Vulnerability details:**
**gas otimization report**
Use calldata instead of memory for function parameters
In some cases, having function arguments in calldata instead of
memory is more optimal.

**POC:**
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/test/utils/Strings2.sol#L4-L9