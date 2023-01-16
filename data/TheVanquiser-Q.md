## Shadowing State Variables

This declaration shadows an existing declaration.
__________________________________________________________________________________________________________________________________________________________________
   --> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L359

  ``` function fileGuardian(File[] calldata file) external { ```

Note: The shadowed declaration is here:
   -->https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L273

  ``` function file(File calldata incoming) public requiresAuth { ```
_______________________________________________________________________________________________________________________________________________________________

--> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L153

``` for (uint256 i = params.encumber.stack.length; i > 0; ) { ```

Note: The shadowed declaration is here:
   --> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L208

``` uint256 i; ```

_______________________________________________________________________________________________________________________________________________________________

 -->  https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L397

``` address currentWithdrawProxy = s ```

Note: The shadowed declaration is here:
   --> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366

``` address currentWithdrawProxy = s ```
_______________________________________________________________________________________________________________________________________________________________

 --> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L717

``` returns (uint256 timeToSecondEpochEnd) ```

Note: The shadowed declaration is here:
   --> https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L722

``` function timeToSecondEpochEnd() public view returns (uint256) { ```
_______________________________________________________________________________________________________________________________________________________________

## 


