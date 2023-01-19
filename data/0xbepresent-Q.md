1 - Public vault creation should be for whitelisted strategists.
==

The [documentation](https://docs.astaria.xyz/docs/threeactors) says: *Whitelisted strategists can deploy PublicVaults that accept funds from other liquidity providers.* but the [newPublicVault()](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L544) function does not have any restriction.

Recommendation
--
Add the creation validation of Public Vault only for whitelisted strategist.

2 - The PrivateVault deposit can be executed even when the vault was paused.
==

There is not restriction for [deposit](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L59) in the Private vault.

Recommendation
--
Add a ```whenNotPaused()``` modifier for the [deposit()](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L59) function.
