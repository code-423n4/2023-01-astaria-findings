SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Code Lines: 
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L672
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65

See https://github.com/code-423n4/2022-01-xdefi-findings/issues/128 which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas
