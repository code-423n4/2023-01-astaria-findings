## Summary
### Low Risk
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| L-01 | fees should be capped                                                            |
| L-02 | Additional sanity checks                                                         |
| L-03 | Add descriptive error strings                                                    |
| L-04 | Avoid leftover ETH in `AstariaRouter`                                            |
| L-05 | Addresses only set in `initialize()` should have appropriate checks              |
| L-06 | Private and Public Vaults can be created with non-existent tokens                |
| L-07 | `PublicVault.minDepositAmount()` can be too high for low decimals token          |
| L-08 | use `_safeMint()` for `LienToken` tokens                                         |
| L-09 | Terms validation in `VaultImplementation` reverts for approved user              |

### Non-Critical
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| N-01 | Naming conventions                                                               |
| N-02 | Scientific notation can be used                                                  |
| N-03 | uppercase should be for constants only                                           |
| N-04 | Emit events in setters                                                           |
| N-05 | Incorrect comment                                                                |
| N-06 | use `bytes.concat()` instead of `abi.encodePacked()`                             |
| N-07 | Avoid shadowing variables                                                        |
| N-08 | Documentation mismatch                                                           |
| N-09 | Constants instead of magic numbers                                               |
| N-10 | `decimals()` is an optional method                                               |
| N-11 | Key parameters changes should be mentioned in the documentation                  |


## Low
### [L‑01] fees should be capped 

The protocol Fee should be capped, as it can currently be set to 100%.
A malicious admin can front run a borrower call to `commitLiens()`, and the latter would not receive any asset as the entire amount would be [transferred as fees](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L394).


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L302
```solidity
302: s.protocolFeeNumerator = numerator.safeCastTo32();
```

Consider also adding an upper boundary to `vaultFee` in `newPublicVault`, to prevent it from being set to higher than 100%

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L571
```solidity
571: vaultFee
```

### [L‑02] Additional sanity checks


Add additional sanity checks to `AstariaRouter._file()`.
if (`denominator == 0` && `numerator ==0`),`getProtocolFee()` would revert, meaning `commitToLiens` would always revert.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L303
```solidity
303:       s.protocolFeeDenominator = denominator.safeCastTo32();
```


```diff
301: if (denominator < numerator) revert InvalidFileData();
+    if(denominator == 0 && numerator ==0) revert();
302:       s.protocolFeeNumerator = numerator.safeCastTo32();
303:       s.protocolFeeDenominator = denominator.safeCastTo32();
```

### [L‑03] Add descriptive error strings

This helps troubleshoot exceptional conditions during transaction failures or unexpected behavior.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339-L342
```solidity
339: function setNewGuardian(address _guardian) external {
340:     RouterStorage storage s = _loadRouterSlot();
341:     require(msg.sender == s.guardian); //@audit - no error string
342:     s.newGuardian = _guardian;
343:     
344:   }
```

### [L‑04] Avoid leftover ETH in `AstariaRouter`


You should check `msg.value == 0` in `pullToken`, as there is no way to retrieve ETH from AstariaRouter if a user mistakenly passes ETH with their call

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L203-L215
```solidity
203: function pullToken(
204:     address token,
205:     uint256 amount,
206:     address recipient
207:   ) public payable override {
208:     RouterStorage storage s = _loadRouterSlot();
209:     s.TRANSFER_PROXY.tokenTransferFrom(
210:       address(token),
211:       msg.sender,
212:       recipient,
213:       amount
214:     );
215:   }
```

### [L‑05] Addresses only set in `initialize()` should have appropriate checks

Add a check to ensure `_VAULT_IMPL != address(0)`.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L101
```solidity
101:   s.implementations[uint8(ImplementationType.PublicVault)] = _VAULT_IMPL;
```

### [L‑06] Private and Public Vaults can be created with non-existent tokens 

At the top of solmate's `SafeTransferLib.sol` is the following comment:

```solidity
9: /// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.

```
The functions safeTransferFrom() and safeTransfer() from SafeTransferLib.sol do not check if a token contract actually contains code. Thus, if the provided token address has no code, these functions will not revert as low-level calls to non-contracts always return success.

This means a malicious actor creating a private vault with a non-existing token (or for instance a new popular token deployed on some EVM side-chain but not on mainnet), a user calling `commitToLien`, would deposit their collateral NFT but never receive any asset in return.


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L519-L520
```solidity
519: ERC20(IAstariaVaultBase(commitments[0].lienRequest.strategy.vault).asset())
520:       .safeTransfer(msg.sender, totalBorrowed);
521:   }
```


Note that the borrower can retrieve their collateral easily for the same reason.


Consider adding a check in `AstariaRouter._newVault()` to ensure the contract exists

```solidity
    require(underlying.code.length > 0, "token is not contract")
```

### [L‑07] `PublicVault.minDepositAmount()` can be too high for low decimals token  

For tokens with decimals != 18, the minimum deposit amount is calculated as `10**(decimals-1)`.

This works for stablecoins, but can be very restrictive for some tokens.

`WBTC` has 8 decimals, and limiting the deposits to a minimum of 0.1 `WBTC` has a current value of ~20k, meaning only deposits of at least ~2k are accepted.

It is much higher than the minimum deposits for tokens of decimals of 18, which is of 100 gwei, ie 10**(ERC20(asset()).decimals() - 9).


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L103-L107
```solidity
103: if (ERC20(asset()).decimals() == uint8(18)) {
104:       return 100 gwei;
105:     } else {
106:       return 10**(ERC20(asset()).decimals() - 1);
107:     }
```

A solution would be to allow a `minDepositAmount` parameter in `newPublicVault()`, allowing the strategist to set the minimum deposits to appropriate values.

### [L‑08] use `_safeMint()` for `LienToken` tokens

Use `_safeMint()` instead of `_mint()` to ensure that `params.receiver` is either an EOA or implements IERC721Receiver

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L455
```solidity
455: _mint(params.receiver, newLienId);
```

### [L‑09] Terms validation in `VaultImplementation` reverts for approved user


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L235-L244

As per the comments of `_validateCommitment()`:

```
220: * Who is requesting the borrow, is it a smart contract? or is it a user?
221:    * if a smart contract, then ensure that the contract is approved to borrow and is also receiving the funds.
222:    * if a user, then ensure that the user is approved to borrow and is also receiving the funds.
```

But looking at the checks:

```
235: address holder = CT.ownerOf(collateralId);
236:     address operator = CT.getApproved(collateralId);
237:     if (
238:       msg.sender != holder &&
239:       receiver != holder &&
240:       receiver != operator &&
241:       !CT.isApprovedForAll(holder, msg.sender)
242:     ) {
243:       revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
244:     }
```

It reverts if the `operator` (ie an approved user) is the `msg.sender`.
To be more precise, in this case it passes only if `receiver == operator`.
This is an extra constraint for the `operator` - while `holder` and an `approvedForAll` user can set any `receiver they want.


Consider adding this extra check:

```diff
237:     if (
238:       msg.sender != holder &&
239:       receiver != holder &&
+          msg.sender != operator &&
240:       receiver != operator &&
241:       !CT.isApprovedForAll(holder, msg.sender)
```

## Non-critical
### [N-01] Naming conventions

Functions should follow naming conventions - use an underscore for internal/private functions


https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L165
```solidity
165: function beforeWithdraw(uint256 assets, uint256 shares) internal virtual {}
```

https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC4626-Cloned.sol#L168
```solidity
168:   function afterDeposit(uint256 assets, uint256 shares) internal virtual {}
```


### [N-02] Scientific notation can be used 


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L604
```solidity
604: uint256 fee = x.mulDivDown(VAULT_FEE(), 10000); //@audit 1e4
```

### [N-03] uppercase should be for constants only


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L97
```solidity
97:   s.COLLATERAL_TOKEN = _COLLATERAL_TOKEN; //@audit can be amended by guardian in `fileGuardian()`
```

### [N-04] Emit events in setters

change of important storage variables should emit an event (here setting the new guardian)

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L353-L358
```solidity
353: function __acceptGuardian() external {
354:     RouterStorage storage s = _loadRouterSlot();
355:     require(msg.sender == s.newGuardian);
356:     s.guardian = s.newGuardian;
357:     delete s.newGuardian;
358:   }
```

### [N-05] Incorrect comment

addresses occupy 20 bytes, the slot ends at 41, not 44.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaVaultBase.sol#L36-L38
```solidity
36: function owner() public pure returns (address) {
37:     return _getArgAddress(21); //ends at 44
38:   }
```

### [N-06] use `bytes.concat()` instead of `abi.encodePacked()`

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L569-L573
```solidity
569: abi.encodePacked(
570:             address(s.ASTARIA_ROUTER),
571:             uint8(IAstariaRouter.ImplementationType.ClearingHouse),
572:             collateralId
573:           )
```

### [N-07] Avoid shadowing variables

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L174
```solidity
174: es.balanceOf[owner] -= shares; //@audit owner is shadowing a storage variable
```

### [N-08] Documentation mismatch 

Duration improvement condition for refinancing is said to be 14 days in part of the docs, and 5 in other parts.


https://docs.astaria.xyz/docs/protocol-mechanics/refinance
```
An improvement in terms is considered if either of these conditions is met:

The loan interest rate decrease by more than 0.05%.
The loan duration increases by more than 5 days.
```

https://docs.astaria.xyz/docs/faq#how-does-refinancing-work
```
A term is deemed to be better if the interest rate is lower than current interest rate or the duration of a loan is longer, by some minimum increase. Currently, valid refinances must either decrease interest rates by 5 basis points or increase loan duration by 14 days
```

### [N-09] Constants instead of magic numbers 


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L604
```solidity
604: uint256 fee = x.mulDivDown(VAULT_FEE(), 10000); //@audit 10000
```

### [N-10] `decimals()` is an optional method 

Quoting [EIP-20](https://eips.ethereum.org/EIPS/eip-20#methods)
```
OPTIONAL - This method can be used to improve usability, but interfaces and other contracts MUST NOT expect these values to be present.
```

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L273
```solidity
273: 10**ERC20(asset()).decimals()
```

### [N-11] Key parameters changes should be mentioned in the documentation

The ability for protocol to change the minimum improvement of interest rate for refinancing should be mentioned in documentation, as it is currently not clear it can be modified.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L314
```solidity
314:       s.minInterestBPS = value.safeCastTo32();
```

