
## UNNECESSARY CHECK IN  LienToken CONTRACT

### Description:

In the modifier validatestack we are already performing the bytes(0) check here https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L268 so there's no need for `stateHash != bytes32(0)` here https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L271

## THE VARIABLE currentWithdrawProxy SHOULD BE RENAMED

### Description:

The variable here https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L366 should be renamed to something else as this 
variable does not depict the current epoch but one epoch before because it's lagging by over the current.

## SAME CHECK DONE TWICE IN A FUNCTION

### Description:

In this function https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L331 we have used the modifier `onlyOwner(collateralId)` and inside the body of the function we have checked ` if (msg.sender != ownerOf(collateralId)) { revert` which does 
the same check as the modifier. One of these should be removed.


## REQUIRE STATEMENT USED IN SPITE OF HAVING A MODIFIER

### Description:

The require statement used here https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L138 should be dropped and 
the modifier onlyVault at https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L230 should be used instead for better 
consistency and readability.


## AVOID THE USE OF TIME-BASED CHECKS USING block.timestamp

### Description:

Values produced by block.timestamp/block.number can be manipulated to some extent therefore services like chainlink or any other decentralised 
oracle should be used . 

For example:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L247 

## NAME THE FUNCTION SOMETHING DIFFERENT TO AVOID CONFUSION

### Description:

Functions here https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L608 and here https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L596 should not be named same as they might induce confusion , name the other something different.
Same with these https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L742 and https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L747

## MISSING DOCSTRINGS

### Description:

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention,
which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve
readability and ease maintenance. They should explicitly explain the purpose or intention of the functions,
the scenarios under which they can fail, the roles allowed to call them, the values returned and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API.
Functions implementing sensitive functionality, even if not public, should be clearly documented as well.
When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

For example  , there is no documentation about the `pullTokens` function in the CollateralToken contract. 

##  NO ZERO ADDRESS/AMOUNT CHECKS

### Description:

No zero address checks in the below mentioned pieces of code. In functions like transfer , mint and deposit if by mistake provided a 0 address , it can lead to permanent loss of funds  , also in functions where we set important roles 0-address checks should be implemented.

Affected Instances:

https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L30-L32 (amount should also be checked if zero)
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L88-L92
https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L29
https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L70
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L170-L171
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L173
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L180
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L281
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L360
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L498
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L17
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L30
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L43
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L57
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L18
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L26
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L37
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L51
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L132
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L163
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L184
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L289
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L119-L120
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L128-L129
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L138
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L233
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L251
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L125
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L140
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L155
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L173
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L190
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L204-L205
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L522
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L544
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L81
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L19
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L40
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L54
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L95
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L210
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L289
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L381


## TYPO IN  Vault CONTRACT

### Description:

There's a typo here  https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L90 ,
change `vautls` to `vaults`



