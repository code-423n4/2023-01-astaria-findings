# QA

## [L-01] Documentation and code mismatch

The documentation is stating that a borrower can take up to 5 loans against the same NFT, but the code is not limiting the borrower. Considering adding a check to limit the number of loans or update the documentation.

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L506)

## [L-02] `init` method is callable multiple times

Upgradable contracts should be initialized only once. Consider adding a check to prevent multiple calls to the init method. Even if the method is technically not callable by anyone and it is setting the delegate address and the allow list. It is still a good practice to add a check to prevent multiple calls.

* [src/contract/VaultImplementation.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L190)


## [L-03] Open TODOs

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L171)
```
// check for rounding error since we round down in previewRedeem.
```

## [L-04] Useless casting

* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L468)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L469)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L708)
* [src/contract/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L268)

```
ERC20 tokenContract = ERC20(tokenAddress);
```

## [L-05] Prefer `external` over public for function not called internally by the contract

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L273)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L402)
* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L331)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L246)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L550)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L562)
* [src/contract/LienToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L706)

## [L-06] Follow the order layout in contract

As mentioned in [solidity documention](https://docs.soliditylang.org/en/v0.8.17/style-guide.html), it is recommended to follow the order layout in contract.
1. Type declarations
2. State variables
3. Events
4. Modifiers
5. Functions

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L196)
* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L460)


## [N-01] NATSPEC IS MISSING

NatSpec is missing for some functions.

* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L234)


## [N-02] Using switch statement instead of if-else

Considering the number of conditions, it is recommended to use switch statement instead of if-else.

* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L281)
* [src/contract/AstariaRouter.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L367)

## [N-03] Use pure instead of view for functions that do not read from storage

* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L82)
* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L90)

## [N-04] Remove functions that are not used

* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L104)
* [src/contract/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L106)

## [N-05] Pragma experimental abiencoderV2 is deprecated

Use pragma abicoder v2 [instead](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics)


* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L16)

## [N-06] require()/revert() statements should have descriptive reason strings

* [src/contract/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L598)
* [src/contract/PublicVault.sol](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L242)