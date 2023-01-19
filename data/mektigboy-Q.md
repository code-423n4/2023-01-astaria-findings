# [QA-01] `ASTARIAROUTER`: CHECKS-EFFECTS-INTERACTIONS RULE NOT APPLIED

Checks-Effects-Interactions rule is not applied in the following code:

```solidity
    //immutable data
    vaultAddr = ClonesWithImmutableArgs.clone(
      s.BEACON_PROXY_IMPLEMENTATION,
      abi.encodePacked(
        address(this),
        vaultType,
        msg.sender,
        underlying,
        block.timestamp,
        epochLength,
        vaultFee
      )
    );

    //mutable data
    IVaultImplementation(vaultAddr).init(
      IVaultImplementation.InitParams({
        delegate: delegate,
        allowListEnabled: allowListEnabled,
        allowList: allowList,
        depositCap: depositCap
      })
    );

    s.vaults[vaultAddr] = true;
```

## Link To Affected Code
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L754

## Recommended Mitigation Steps
Place `s.vaults[vaultAddr] = true;` before `IVaultImplementation(vaultAddr).init(...);`.