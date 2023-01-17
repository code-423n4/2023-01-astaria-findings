## Title
Missing sanity check for amount input at the function drain()
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L289

## Tools Used
Manual

## Recommended Mitigation Steps
Consider Add require(amount > 0, "amount connot be 0 or negative")