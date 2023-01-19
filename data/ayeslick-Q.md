https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L334
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L338


The `releaseAddress` function checks if the msg.sender is the owner of the collateral token twice.

Recommendation:
Check if msg.sender is the owner of the collateral token once

