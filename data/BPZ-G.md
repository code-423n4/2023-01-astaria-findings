## Gas Report

## Use Custom Errors
Custom errors are more gas efficient than using require with a string explanation. So ideally you'd always use this over require.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65

https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L71

## Functions guaranteed to revert when called by normal users can be marked payable

if a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L70


## Empty blocks should be removed or emit something

https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L87
