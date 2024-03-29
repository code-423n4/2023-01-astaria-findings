## Comments and code mismatch
As denoted by the [comments on `commitToLiens()`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L174-L181) in IAstariaRouter.sol:

```
  /**
   * @notice Deposits collateral and requests loans for multiple NFTs at once.
   * @param commitments The commitment proofs and requested loan data for each loan.
   * @return lienIds the lienIds for each loan.
   */
```
However, the [logic entailed](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L490-L516) in AstariaRouter.sol only deals with one NFT and multiple, NOT multiple NFTs. Consider updating the NatSpec associated to better reflect the intended design of this particular function.

## Solmate's contract existence check
As denoted by [File: SafeTransferLib.sol#L9](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9):

```
/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.
```
As such, the following token transfers associated with Solmate's `SafeTransferLib.sol`, are some of the instances performing [low-level calls without confirming contract’s existence that could return success even though no function call was executed](https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions):

[File: TransferProxy.sol#L34](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L34)

```solidity
    ERC20(token).safeTransferFrom(from, to, amount);
```
[File: Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

```solidity
66:    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);

72:    ERC20(asset()).safeTransferFrom(address(this), msg.sender, amount);
```
## Camel casing input parameter names
Input parameter names should be conventionally camel cased where possible.

Here are some of the instances entailed:

[File: TransferProxy.sol#L24](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L24)

```diff
-  constructor(Authority _AUTHORITY) Auth(msg.sender, _AUTHORITY) {
+  constructor(Authority _authority) Auth(msg.sender, _authority) {
```
## Camel/snake casing function names
Function names should also be conventionally camel cased where possible. If snake case is preferred/adopted, consider lower casing them.

Here are some of the instances entailed:

[File: AstariaVaultBase.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaVaultBase.sol)

```
50:  function START() public pure returns (uint256) {

54:  function EPOCH_LENGTH() public pure returns (uint256) {

58:  function VAULT_FEE() public pure returns (uint256) {

62:  function COLLATERAL_TOKEN() public view returns (ICollateralToken) {
```
## Wrong method called
`name()` in Vault.sol, WithdrawProxy.sol, and PublicVault.sol have their ERC20 instance externally and erroneously calling `symbol()` instead of `name()`.

Consider having their respective errors fixed as follows:

[File: Vault.sol#L28-L36](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L28-L36)

```diff
  function name()
    public
    view
    virtual
    override(VaultImplementation)
    returns (string memory)
  {
-    return string(abi.encodePacked("AST-Vault-", ERC20(asset()).symbol()));
+    return string(abi.encodePacked("AST-Vault-", ERC20(asset()).name()));
  }
```
[File: WithdrawProxy.sol#L103-L111](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L103-L111)

```diff
  function name()
    public
    view
    override(IERC20Metadata, WithdrawVaultBase)
    returns (string memory)
  {
    return
-      string(abi.encodePacked("AST-WithdrawVault-", ERC20(asset()).symbol()));
+      string(abi.encodePacked("AST-WithdrawVault-", ERC20(asset()).name()));
  }
```
[File: PublicVault.sol#L76-L84](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L76-L84)

```diff
  function name()
    public
    view
    virtual
    override(IERC20Metadata, VaultImplementation)
    returns (string memory)
  {
-    return string(abi.encodePacked("AST-Vault-", ERC20(asset()).symbol()));
+    return string(abi.encodePacked("AST-Vault-", ERC20(asset()).name()));
  }
```
## string.concat over abi.encodePacked
Solidity ^0.8.12 introduced `string.concat()` featuring better code readability than `abi.encodePacked()`.

Here are some of the instances entailed:

[File: Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

```diff
- 35:    return string(abi.encodePacked("AST-Vault-", ERC20(asset()).symbol()));
+ 35:    return string.concat("AST-Vault-", ERC20(asset()).symbol());

- 46:      string(abi.encodePacked("AST-V", owner(), "-", ERC20(asset()).symbol()));
+ 46:      string.concat("AST-V", owner(), "-", ERC20(asset()).symbol());
```
## Non-compliant contract layout with Solidity's Style Guide
According to Solidity's Style Guide below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

In order to help readers identify which functions they can call, and find the constructor and fallback definitions more easily, functions should be grouped according to their visibility and ordered in the following manner:

constructor, receive function (if exists), fallback function (if exists), external, public, internal, private

And, within a grouping, place the `view` and `pure` functions last.

Additionally, inside each contract, library or interface, use the following order:

type declarations, state variables, events, modifiers, functions

Consider adhering to the above guidelines for all contract instances entailed in the code bases.

## Typo mistakes
[File: Vault.sol#L90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L90)

```diff
-    //invalid action private vautls can only be the owner or strategist
+    //invalid action private vaults can only be the owner or strategist
```
## Empty blocks
Function with empty block should have a comment explaining why it is empty, or an event emitted.

Here are some of the instances entailed:

[File: ClearingHouse.sol#L104](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104)

```solidity
  function setApprovalForAll(address operator, bool approved) external {}
```
[File: ClearingHouse.sol#L180-L186](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L180-L186)

```solidity
  function safeBatchTransferFrom(
    address from,
    address to,
    uint256[] calldata ids,
    uint256[] calldata amounts,
    bytes calldata data
  ) public {}
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the `a = false` instance below may be refactored as follows:

[File: VaultImplementation.sol#L106](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L106)

```diff
-    _loadVISlot().allowListEnabled = false;
+    delete _loadVISlot().allowListEnabled;
```
## Deprecated code
As denoted in [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html#silent-changes-of-the-semantics):

"... The pragma pragma experimental ABIEncoderV2; is still valid, but it is deprecated and has no effect. If you want to be explicit, please use pragma abicoder v2; instead."

Consider the removing the deprecated code line in the contract instance below:

[File: CollateralToken.sol#L16](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L16)

```diff
- pragma experimental ABIEncoderV2;
```
## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

The code bases are in general lacking partial/full NatSpec that will be of added values to the users and developers if adequately provided. 

## Empty Event
The following event has no parameter in it to emit anything. Although the standard global variables like block.number and block.timestamp will implicitly be tagged along, consider adding some relevant parameters to the make the best out of this emitted event for better support of off-chain logging API.

[File: VaultImplementation.sol#L149](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L149)

```solidity
    emit VaultShutdown();
```
## Imported library not fully utilized
SafeCastLib.sol imported in AstariaRouter should be fully utilized, serving also as a measure to synchronize all related casting styles.

[File: AstariaRouter.sol#L112](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L112)

```diff
-    s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
+    s.minInterestBPS = ((uint256(1e15) * 5) / (365 days)).safeCastTo32;
```
## Erroneous `uint256()` cast
[`stack[position].lien.details.rate`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L54), a uint256 variable, is cast into `uint256()` in AstoriaRouter.sol. This cast should have been done on [`s.minInterestBPS`](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L74), a uint32 variable:

[File: AstariaRouter.sol#L690-L691](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L690-L691)

```diff
-    uint256 maxNewRate = uint256(stack[position].lien.details.rate) -
-      s.minInterestBPS;
+    uint256 maxNewRate = stack[position].lien.details.rate -
+      uint256(s.minInterestBPS);
```
## `return` statement on named returns
`_executeCommitment()` in AstariaRouter.sol adopts named returns but still resort to using `return` in its function logic. Consider removing the names to return values or the opposite manner but not the hybrid of both to avoid any unnecessary confusion.

[File: AstariaRouter.sol#L761-L786](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L761-L786)

```diff
  function _executeCommitment(
    RouterStorage storage s,
    IAstariaRouter.Commitment memory c
  )
    internal
    returns (
      uint256,
-      ILienToken.Stack[] memory stack,
+      ILienToken.Stack[] memory,
-      uint256 payout
+      uint256
    )
  {
    uint256 collateralId = c.tokenContract.computeId(c.tokenId);

    if (msg.sender != s.COLLATERAL_TOKEN.ownerOf(collateralId)) {
      revert InvalidSenderForCollateral(msg.sender, collateralId);
    }
    if (!s.vaults[c.lienRequest.strategy.vault]) {
      revert InvalidVault(c.lienRequest.strategy.vault);
    }
    //router must be approved for the collateral to take a loan,
    return
      IVaultImplementation(c.lienRequest.strategy.vault).commitToLien(
        c,
        address(this)
      );
  }
```