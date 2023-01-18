
## LOW

#### The event AllowListUpdated is emitted but the allowList is not updated
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L215
At above line the event AllowListUpdated is triggered, but the function `modifyAllowList(delegate_, true)` is not called and neither required.


#### Compiler warnings
Compiler warnings should be not ignored, can lead to undesired effects. I suggest at least to fix the following one that could cause issues
src/AstariaRouter.sol:359:25: `function fileGuardian(File[] calldata file)`
This declaration shadows an existing declaration: src/AstariaRouter.sol:273:3: `function file(File calldata incoming) public requiresAuth`

#### Unused function parameters
Unused function parameter. Remove or comment out the variable name to silence the warnings.
src/ClearingHouse.sol:82
src/ClearingHouse.sol:90
src/ClearingHouse.sol:106
src/ClearingHouse.sol:115 and 116
src/ClearingHouse.sol:174
src/ClearingHouse.sol:189, 190 and 191
src/LienToken.sol:775
src/CollateralToken.sol:159 and 160
src/CollateralToken.sol:173 175 and 176
src/WithdrawProxy.sol:143
src/WithdrawProxy.sol:147
src/PublicVault.sol:413
src/PublicVault.sol:575
src/security/V3SecurityHook.sol:25

#### Duplicated require()/revert() checks should be refactored to a modifier or function
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L78
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.soll#L96
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L105
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L114
`require(msg.sender == owner());` can be replaced by a modifier
I tested that it doesn't save gas but it keeps code a little bit more neat

#### Wrong returns parameter
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L717
`timeToSecondEpochEnd` in returns statement is useless

     function _timeToSecondEndIfPublic()  internal view override returns (uint256 timeToSecondEpochEnd)
     { return timeToEpochEnd() + EPOCH_LENGTH();}

#### Unused struct declaration
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ICollateralToken.sol#L68
`ListUnderlyingForSaleParams` struct seem to be unused in the contract, moreover maxDuration type seems to be a typo
  
    struct ListUnderlyingForSaleParams {
       ILienToken.Stack[] stack;
       uint256 listPrice;
       uint56 maxDuration; }




##INFORMATIONAL

#### Wrong comments
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L37 - 44 should be 41
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L47 - 64 should be 61
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L55 - 116 should be 125

#### Suggest to change function from newVault() to newPrivateVault()
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L521
This change would make it easier for a newcomer to understand differences between publicVault and privateVault entry point.

#### NatSpec parameters are wrong
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L166
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/ILienToken.sol#L94, L138 and L145
In the above lines NatSpec is wrong due to copy&paste error




