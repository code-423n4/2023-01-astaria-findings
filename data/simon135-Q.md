#####  There is no  2-step ownership in this function,make an  function for the new owner to accept ownership
```solidity
  function transferOwnership(address newOwner) public virtual requiresAuth {
    AuthStorage storage s = _getAuthSlot();
    s.owner = newOwner;

    emit OwnershipTransferred(msg.sender, newOwner);
  }
```
https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/AuthInitializable.sol#L97-L100
##### typos
instead of  calcualtion: make it: calculation
https://github.com/code-423n4/2023-01-astaria/blob/40065677771348dbfde8c1ca442825ae37e2c3d0/src/PublicVault.sol#L307

instead of vautls : make it: vaults
https://github.com/code-423n4/2023-01-astaria/blob/feb342a6666b9a97ef16d25151e020281acb1f5f/src/Vault.sol#L90
instead  of acheive:  make it: achieve
https://github.com/code-423n4/2023-01-astaria/blob/e3c463001a7c28caaa7d922de47c7f108b7e46de/src/interfaces/IV3PositionManager.sol#L122-L123

