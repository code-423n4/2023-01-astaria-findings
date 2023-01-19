1. Local variable shadows function
** local variable owner in PublicVault._redeemFutureEpoch() shadows function AstariaVaultBase.owner()
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L152
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol#L36

**local variable `timeToSecondEpochEnd` in PublicVault._timeToSecondEndIfPublic()  shadows function PublicVault.timeToSecondEpochEnd()
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L717
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L722


2. Unused return local variable
** The return local variable `timeToSecondEpochEnd` in PublicVault._timeToSecondEndIfPublic() is unused in the return. 
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L717