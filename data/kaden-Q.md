#### Incorrect inline documentation

##### Severity: Low

##### Description: 

- AstariaVaultBase.sol contains incorrect comments as to where the args end
- VaultImplementation._validateCommitment documents that the party approved to borrow must receive the funds but this is not enforced in the code
- Misplaced comments in PublicVault._redeemFutureEpoch
- Missing natspec parameters in AstariaRouter._newVault


#### Unused variable

##### Severity: Low

##### Context: 

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L262

##### Description:

`assets` is unused.


#### `SafeTransferLib` may allow code to succeed with a non-existent token contract

##### Severity: Low

##### Description: 

As documented in `solmate/SafeTransferLib.sol`:

```
/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.
```

`SafeTransferLib` uses low level calls to interact with the token contracts. Low level calls will succeed if there is no code, which can lead to methods proceeding successfully when they ought not to.


#### `_safeMint` should be used rather than `_mint` wherever possible

##### Severity: Low

##### Description: 

`_mint` is discouraged in favor of `_safeMint` which ensures that the recipient is either an EOA or implements IERC721Receiver. Both OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.