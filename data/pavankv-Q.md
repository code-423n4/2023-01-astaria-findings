## 1 . Unprotected and front-runable initialize() :-

miner or other bad actor can front-run this function so add access control .

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59

## 2. No checks for arrays :-

In balanceOfBatch() there is no checks for address[] calldata accounts, uint256[] calldata ids.

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L171

recommedation :-
require(address[] calldata accounts==uint256[] calldata ids , "error");

## 3 . Modifiers should be top in file or before function :-

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L602
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L679