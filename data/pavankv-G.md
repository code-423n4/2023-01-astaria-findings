## 1 . Split require statement instead of && :-
&& consumes 3 gas so it's good practice to use two require.

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65

## 2 Reduce the revert string :-
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L134


## 3 . Use immutable to keecak operation :-
When variable is declare with keccak operation try to declare as immutable because keccak operation consumes some gas to convert string to hash at each time call .So declare has immutable it will convert at time of deploy and in immutable state saves operation cost.

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L49
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L40'

## 4 use x=x+y instead of x += y for stroage varible to save gas :-

code snippet:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L237
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L313

