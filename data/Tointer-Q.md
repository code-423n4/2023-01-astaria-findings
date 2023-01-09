1. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L106 
minimum deposit amount for tokens with non standart decimals value are too high. 0.1 can be quite a lot for tokens with small totalAmount, so this requirement can become too restrictive. For example, WBTC token have 8 decimals, so minimum deposit for it would be around $1.7k.
2. https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L262 this line can be removed
