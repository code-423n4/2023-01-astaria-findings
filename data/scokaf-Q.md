# Number 1: Floating Pragma

Vulnerability details

## Impact

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.17, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

For reference, see https://swcregistry.io/docs/SWC-103

## Proof of Concept

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626Router.sol#L2

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626RouterBase.sol#L2

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol#L2

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol#L1

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626Router.sol#L2

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IERC4626RouterBase.sol#L2


## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Remove ^ in “pragma solidity ^0.8.17” and change it to “pragma solidity 0.8.17” to be consistent with the rest of the contracts.

# Number 2: Block values as a proxy for time

Vulnerability details

## Impact

Some contracts use block.timestamp and block.number which can be problematic as Miners can alter block.timestamp with the following restrictions.

It cannot bear a time stamp that is earlier than that of its parent.
It won't be too long from now.

## Proof of Concept

WithdrawProxy.sol
Block.timestamp - L314

CollateralToken.sol
Block.number - L471

PublicVault.sol
Block.timestamp - L706, L710

AsteriaRouter.sol
Block.timestamp - L617, L698, L700

LienToken.sol
Block.timestamp - L247, 335, 336, 452, 453, 475, 590, 780, 808, 827

ERC20_Cloned.sol
Block.timestamp - L115


## Tools Used

Manual Analysis

### Recommended Mitigation Steps

1. Don't use block.timestamp for a source of entropy and random number
2. Use of trusted oracles


# Number 3: More Readable Values 

Vulnerability details

## Impact

A uint value is difficult to read at one time because it has a lot of 0's.
Solidity allows _ to separate series of zero's

## Proof of Concept

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L604 

## Tools Used

Manual Analysis

## Recommended Mitigation Steps

Replace 10000 with 10_000

