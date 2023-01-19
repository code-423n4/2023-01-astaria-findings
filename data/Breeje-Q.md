## QA Report

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | NO ZERO ADDRESS CHECK | 2 |
| [L-2](#L-2) | EVENT EMITTED WITHOUT IMPLEMENTING | 1 |
| [L-3](#L-3) | DANGEROUS FUNCTION | 1 |
| [NC-1](#NC-1) | UNUSED PARAMETER CAN BE REMOVED | 1 |
| [NC-2](#NC-2) | NATSPEC MISSING | 4 |
| [NC-3](#NC-3) | MISLEADING VARIABLE NAME | 1 |


### [L-1] NO ZERO ADDRESS CHECK

There should be a zero address check before assigning important address variable.

*Instances (2):*
```solidity
File: AstariaRouter.sol

339:       function setNewGuardian(address _guardian) external {

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L339)

```solidity
File: VaultImplementation.sol

210:       function setDelegate(address delegate_) external {

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L210)

### [L-2] EVENT EMITTED WITHOUT IMPLEMENTING

In `setDelegate` method, delegate is updated but Allowlist haven't been Updated. Still `AllowListUpdated` Event has been emitted.

```solidity
File: VaultImplementation.sol

215:       emit AllowListUpdated(delegate_, true);

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L215)

### [L-3] DANGEROUS FUNCTION

Using `__renounceGuardian` method in `AstariaRouter.sol` can be dangerous for Protocol as it sets both Guardian and newGuardian to Zero address which can never recover. So all the function which can only be called by guardian will be locked forever.

```solidity
File: VaultImplementation.sol

345:       function __renounceGuardian() external {

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L345)

### [NC-1] UNUSED PARAMETER CAN BE REMOVED

The parameter `tokenContract` is not used at all in the function.

```solidity
File: security/V3SecurityHook.sol

25:       function getState(address tokenContract, uint256 tokenId)

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/src/security/V3SecurityHook.sol.sol#L25)

### [NC-2] NATSPEC MISSING

SPDX license identifier not provided.

*Instance (4):*
* [IERC20Metadata.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC20Metadata.sol)
* [IERC721.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC721.sol)
* [IV3PositionManager.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IV3PositionManager.sol)
* [Base64.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/libraries/Base64.sol)

### [NC-3] MISLEADING VARIABLE NAME

`maxSharesIn` should be used in place of `maxSharesOut`.

```solidity
File: lib/gpl/src/ERC4626RouterBase.sol

45:       uint256 maxSharesOut

```
[Link to Code](https://github.com/code-423n4/2023-01-astaria/blob/main/lib/gpl/src/ERC4626RouterBase.sol#L45)