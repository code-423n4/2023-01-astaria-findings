## Unused variable, should use storage to save MSTORE
tokenContract and tokenId was defined but not used. underlying also dont need to declare, we can use s.idToUnderlying[collateralId] directly

### Proof of Concept
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L137-L142

### Recommended Mitigation Steps
```solidity
_releaseToAddress(s, s.idToUnderlying[collateralId], collateralId, liquidator);
```