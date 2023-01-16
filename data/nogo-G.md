# Gas optimizations

## [G-01] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L476)


## [G-02] Functions guaranteed to revert when called by normal users can be marked payable

No additional checks are required since the function can accept both zero or non-zero values of ether. 

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L259)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L259)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L273)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L352)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L552)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L621)

## [G-03] Reject earlier when possible

`_validateCommitment` can be called at the begining of the function to save gas. This will also improve readability.

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L595)

## [G-04] Unused imports can be removed

* [src/contract/AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L21)
* [src/contract/AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L17)
* [src/contract/AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L26)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L33)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L53)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L34)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L38)

## [G-05] Use custom errors rather than rever()/require() strings to save gas

Custom errors are available from solidity version 0.8.0. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L72)
* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L134)
* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L199)
* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L222)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L266)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L535)
* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L170)

## [G-05] Using bool for storage brings overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L402)

## [G-06] Use assembly for address 0 check

Example
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L330)

## [G-07] Internal function never used can be removed

Try to keep the number of internal and private functions to a minimum to balance function complexity and quantity. In turn, this will help you reduce gas fees upon execution by reducing the number of function calls. However, keep in mind that having too large functions makes testing more difficult and even jeopardizes security.

* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L541)

```
function _settleAuction(CollateralStorage storage s, uint256 collateralId)
  internal
{
  delete s.collateralIdToAuction[collateralId];
}
```

## [G-08] Use named returns for local variables

* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L258)
* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L718)
* [src/contract/VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L357)

## [G-09] Remove unused variables

* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L263)