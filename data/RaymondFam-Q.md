## Solmate's contract existence check
As denoted by [File: SafeTransferLib.sol#L9](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9):

```
/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.
```
As such, the following token transfers associated with Solmate's `SafeTransferLib.sol`, are performing [low-level calls without confirming contract’s existence that could return success even though no function call was executed](https://docs.soliditylang.org/en/v0.8.7/control-structures.html#error-handling-assert-require-revert-and-exceptions):

[File: TransferProxy.sol#L34](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L34)

```solidity
    ERC20(token).safeTransferFrom(from, to, amount);
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
`name()` in Vault.sol has its ERC20 instance externally and erroneously calling `symbol()` instead of `name()`.

Consider having the error fixed as follows:

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