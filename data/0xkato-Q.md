N-1
function should be removed 
Its called ones but does not return anything 
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/BeaconProxy.sol#L87

N-2
To long line
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L42
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L282
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L363
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L54

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ICollateralToken.sol#L100
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L27
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L33
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IWithdrawProxy.sol#L35
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L75

N-3
Typo 
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L831
 //      // slope =>  // slope
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L119

N-5
Use require when modifier applied 
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L338-L340
The modifier
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L334

N-6
Use require when modifier already made but not used
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L138
The modifier
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L230-L233
the  CollateralToken of the NFT => the CollateralToken of the NFT

N-7
Missing require to prevent underflow 
Migration 
Add require(es.balanceOf[owner] >= shares);
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L173-L174

N-8
TODO comment
should be resolved before deployment
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/test/WithdrawTesting.t.sol#L173
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/test/WithdrawTesting.t.sol#L342

N-9
inconsistent spacing in comments
Some lines uses //x and some uses // x
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L126
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L155

https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AstariaRouter.sol#L780
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AstariaRouter.sol#L65

https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L173
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L176

https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/CollateralToken.sol#L297
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/CollateralToken.sol#L305
Instances show is proof not all Instances

N-10
confusing comments styles
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L52-L63
vs 
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IUniswapV3PoolState.sol#L81-L87 
N-11 
Misleading comment 
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L140-L142
@return the amount owed in "uint192" at the current block.timestamp
function is returning uint88
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L148-L153
@return the amount owed in uint192
function is returning uint88

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L148-L153

L-1
Strange function behaviour 
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/ClearingHouse.sol#L82-L88
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/ClearingHouse.sol#L90-L102
Should return the something more suitable 

L-2
Unused function perimeter 
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/ClearingHouse.sol#L169-L178
Add 
    if(data == 0){
    _execute(from, to, identifier, amount);
    } 
    else {
      // do something else or revert 
    }

L-3
Function returns empty string 
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L348-L358

L-4
Missing address(0) when transferring ownership
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/AuthInitializable.sol#L95-L99