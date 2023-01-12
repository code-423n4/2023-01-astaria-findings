## 1. File does not contain an SPDX Identifier

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC721.sol
https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/interfaces/IWETH9.sol


## 2. Use safeTransfer/safeTransferFrom consistently instead of transfer/transferFrom

It is good to add a require() statement that checks the return value of token transfers or to use something like OpenZeppelin’s safeTransfer/safeTransferFrom unless one is sure the given token reverts in case of a failure. Failure to do so will cause silent failures of transfers and affect token accounting in contract.

Reference: This similar medium-severity finding from Consensys Diligence Audit of Fei Protocol: https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call


## 3. Consider adding checks for signature malleability

[SWC-117](https://swcregistry.io/docs/SWC-117):
"The implementation of a cryptographic signature system in Ethereum contracts often assumes that the signature is unique, but signatures can be altered without the possession of the private key and still be valid. The EVM specification defines several so-called ‘precompiled’ contracts one of them being `ecrecover` which executes the elliptic curve public key recovery. A malicious user can slightly modify the three values v, r and s to create other valid signatures. A system that performs signature verification on contract level might be susceptible to attacks if the signature is part of the signed message hash. Valid signatures could be created by a malicious user to replay previously signed messages."

Use OpenZeppelin’s `ECDSA` contract rather than calling `ecrecover()` directly.

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L121-L141
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L246-L257


## 4. Function order

Functions should be ordered following [the Solidity conventions](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions):

"Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private"

In a lot of contracts this best practice has not been followed.


## 5. Avoid nested `if` statements

For better readability and analysis it is better to avoid nested `if` blocks.

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L62-L68
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L212-L216
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L438-L446
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L829-L834
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L367-L391
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L159-L165
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L291-L301
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L371-L382
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L306-L313
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L563-L582


## 6. `_safeMint()` should be used rather than `_mint()` wherever possible

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. Both OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.


https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L588
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L31
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L47
https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L455
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L512

