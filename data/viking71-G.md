# VISIBILITY: PUBLIC FUNCTIONS TO EXTERNAL

## Impact
More gas is used.

## Description
Some functions are set `public` visibility even though they are not used within the contract. These functions can be set to `external` visibility because external function's call cost is less expensive than of public functions.

## Code Instances
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC20-Cloned.sol#L114

## Recommendations
Changing the visibility to external will save gas and improve code quality. 