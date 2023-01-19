
## 1. expressions for constant values such as a call to keccak256(), should use immutable rather than constant

- [VaultImplementation.sol#L44](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L44)
- [VaultImplementation.sol#L47](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L47)
- [VaultImplementation.sol#L51](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L51)


## 2. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [WithdrawProxy.sol#L237](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L237)
- [WithdrawProxy.sol#L313](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L313)

- [PublicVault.sol#L179](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L179)
- [PublicVault.sol#L565](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L565)
- [PublicVault.sol#L583](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L583)
- [PublicVault.sol#L606](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L606)
- [PublicVault.sol#L624](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L624)
- [PublicVault.sol#L174](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L174)
- [PublicVault.sol#L380](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L380)
- [PublicVault.sol#L405](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L405)


## 3. can make the variable outside the loop to save gas

make the variables outside and only give them value inside

`what`
- [AstariaRouter.sol#L365](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L365)

`data`
- [AstariaRouter.sol#L365](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L365)

`payout`
- [AstariaRouter.sol#L510](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L510)

`j`
- [LienToken.sol#L154](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L154)
- [LienToken.sol#L473](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L473)

`auctionStack`
- [LienToken.sol#L305](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L305)

`owed`
- [LienToken.sol#L309](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L309)

`payee`
- [LienToken.sol#L313](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L313)

`spent`
- [LienToken.sol#L521](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L521)
- [LienToken.sol#L671](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L671)

`oldLength`
- [LienToken.sol#L670](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L305)


## 4. splitting require() statements that use `&&` saves gas

this will have a large deployment gas cost but with enough runtime calls the split require version will be 3 gas cheaper

- [PublicVault.sol#L672](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672)
- [PublicVault.sol#L678](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L678)


## 5. switch the sides of or operation to possibly avoid using more gas

There is a chance that the first part will be true so the second part doesn’t need to be checked, so it is better to use the part that we have instead of the part that needs to be called.

switch the sides because we already have `liquidator`
- [CollateralToken.sol#L116-L118](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L116-L118)

we have `recovered` so switch
- [VaultImplementation.sol#L258](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L258)


## 6. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_execute`
- [ClearingHouse.sol#L114](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L114)

`_generateValidOrderParameters`
- [CollateralToken.sol#L425](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L425)

`_listUnderlyingOnSeaport`
- [CollateralToken.sol#L502](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L502)

`_settleAuction`
- [CollateralToken.sol#L541](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L541)

`_increaseOpenLiens`
- [PublicVault.sol#L556](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L556)

`_handleStrategistInterestReward`
- [PublicVault.sol#L597](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L597)

`_executeCommitment`
- [AstariaRouter.sol#L761](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L761)

`_transferAndDepositAssetIfAble`
- [AstariaRouter.sol#L788](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L788)


## 7. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

`ROUTER()`
- [ClearingHouse.sol#L43](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L43)

`IMPL_TYPE()`
- [ClearingHouse.sol#L51](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L51)

`safeBatchTransferFrom()`
- [ClearingHouse.sol#L186](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L186)

`getFinalAuctionEnd`
- [WithdrawProxy.sol#L215](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L215)

`getWithdrawRatio()`
- [WithdrawProxy.sol#L220](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L220)

`getWithdrawRatio()`
- [WithdrawProxy.sol#L225](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L215)

` file()`
- [CollateralToken.sol#L206](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L206)

`getConduitKey`
- [CollateralToken.sol#L370](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L370)


## 8. pre calculate state vars

pre calculate type casts and operations and only give the result to variables instead of doing it at the start of the contracts every time the contract is made

- [LienToken.sol#L53](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L53)

pre calculate the `uint256(keccak256("some string")) - 1` and give the answer to the constant
- [ClearingHouse.sol#L40](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L40)
- [WithdrawProxy.sol#L49](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L49)
- [CollateralToken.sol#L73](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L73)
- [PublicVault.sol#L53](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L53)
- [AstariaRouter.sol#L62](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L62)
- [LienToken.sol#L50](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L50)
- [VaultImplementation.sol#L57](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L57)

pre calculate keccak256()
- [VaultImplementation.sol#L44](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L44)
- [VaultImplementation.sol#L47](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L47)
- [VaultImplementation.sol#L51](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L51)
