
## [L-01] Typo in Comments

```solidity

File: Vault.sol
Line: 90

function modifyAllowList(address, bool)
    external
    pure
    override(VaultImplementation)
  {
    //invalid action private vautls can only be the owner or strategist
    revert InvalidRequest(InvalidRequestReason.NO_AUTHORITY);
  }
```

The comment should be `vaults` instead of `vautls`