## Low

### 1. `initialize` can be called by anybody.

`initialize()` function can be called anybody when the contract is not initialized.

In the `AstariaRouter.sol`, anybody can call the function which then calls `__initAuth` with `msg.sender` as a parameter which then sets the owner to the caller.

It is the same in `CollateralToken.sol` and in `LienToken.sol`.

See: 
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L83
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L80
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L59

#### Recommended Mitigation Steps

Add a control that makes `initialize()` only be called by the deployer contract or an admin controlled address.