### Gas Optimizations 

| | issue |
| ----------- | ----------- |
| 1 | [Usage of uint/int smaller than 32 bytes incurs overhead](#1-Usage-of-uint-int-smaller-than-32-bytes-incurs-overhead) |
| 2 | [Change constant to Immutable for keccak Variables when they are not used in the constructor](#2-Change-constant-to-Immutable-for-keccak-Variables-when-they-are-not-used-in-the-constructor) |
| 3 | [Add `unchecked {}` for subtractions where the operands cannot over/underflow.](#3-add-unchecked--for-subtractions-where-the-operands-cannot-over/underflow) |
| 4 | [10 ** decimal can be changed to 18 e decimal  and save some gas](#4-10-**-decimal-can-be-changed-to-18-decimal-and-save-some-gas) |
| 5 | [Use Double require instead of operator &&. This will save 10 gas with the optimizer enabled](#5-Use-Double-require-instead-of-operator-&&.-This-will-save-10-gas-with-the-optimizer-enabled) |
| 6 | [Duplicated require()/revert() checks should be refactored to a modifier or function (instances)](#6-Duplicate-require()/revert()-checks-should-be-refactored-to-a-modifier-or-function-(instances)) |



## 1. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead
***

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed [Layout in Storage](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html).


- [AstariaRouter.sol#L621](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L621)

- [AstariaRouter.sol#L686](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L686)

- [LienToken.sol#L611](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L611)

- [LienToken.sol#L611](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L793)


## 2. Change constant to Immutable for keccak Variables when they are not used in the constructor
***

The use of constant keccak variables results in extra hashing (and so gas). This results in the keccak operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash. Changing to immutable will only perform hashing on contract deployment which will save gas. You should use immutables until the referenced issues are implemented, then you only pay the gas costs for the computation at deploy time.

- [AstariaRouter.sol#L62](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L62)

- [ClearingHouse.sol#L40-L41](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L40-L41)

- [CollateralToken.sol#L73-L74](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L73-L74)

- [LienToken.sol#L50-L51](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L50-L51)

- [PublicVault.sol#L53-L54](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L53-L54)

- [VaultImplementation.sol#L44-L51](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L44-L51)

- [VaultImplementation.sol#L57](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L57)

- [WithdrawProxy.sol#L49-L50](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L49-L50)

## 3. Add `unchecked {}` for subtractions where the operands cannot over/underflow.
***

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block see resource `require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }` This will stop the check for overflow and underflow so it will save gas

- [LienToken.sol#L154](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L154)

- [LienToken.sol#L193-L194](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L193-L194)

- [LienToken.sol#L473](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L473)

- [LienToken.sol#L638](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L638)

- [LienToken.sol#L830](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L830)

## 4. 10 ** decimal can be changed to 18 e decimal  and save some gas
***

- [PublicVault.sol#L106](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L106)

- [WithdrawProxy.sol#L273](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L273)

## 5. Use Double require instead of operator &&. This will save 10 gas with the optimizer enabled
***

- [PublicVault.sol#L687-L690](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L687-L690)

- [PublicVault.sol#L672-L675](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L672-L675)

- [Vault.sol#L65](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65)

## 6. Duplicated require()/revert() checks should be refactored to a modifier or function (instances)
***
The below shown require statement is used 6 times within the same contract. 

    `require(msg.sender == owner()); //owner is "strategist"`


- [VaultImplementation.sol#L78](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L78)

- [VaultImplementation.sol#L96](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L96)

- [VaultImplementation.sol#L105](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L105)

- [VaultImplementation.sol#L114](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L114)

- [VaultImplementation.sol#L147](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L147)

- [VaultImplementation.sol#L211](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L211)