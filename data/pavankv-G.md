## 1 . Split require statement instead of && :-
&& consumes 3 gas so it's good practice to use two require.

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687

## 2 Reduce the revert string :-
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L134


## 3 . Use immutable to keecak operation :-
When variable is declare with keccak operation try to declare as immutable because keccak operation consumes some gas to convert string to hash at each time call .So declare has immutable it will convert at time of deploy and in immutable state saves operation cost.

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L49
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L40
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L53
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L62
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L50

## 4 use x=x+y instead of x += y for stroage varible to save gas :-

code snippet:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L174
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L179
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L237
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L313
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L405
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L380
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L565
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L583
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L624


## 5. Function which doesn't called by  contract can be declare as external to save gas :-

code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L126
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L251

## 6 . Use ternary operation :-
code snippet:-
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L706

Old :-
  if (block.timestamp >= epochEnd) {
      return uint256(0);
    }

    return epochEnd - block.timestamp;
  }

New :-
  return (block.timestamp >= epochEnd) ?return uint(0) :(epochEnd-block.timestamp); 
     


