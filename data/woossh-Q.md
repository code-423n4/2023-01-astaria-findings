https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L240

The claim() function in the WithdrawProxy.sol smart contract is used to claim the share of a deposit. The claim can be done only once the final auction is over.
However, the claim() function does not verifiy whether the claim has been done already. As such, it is possible for an attacker to claim an unlimited amount of time.
