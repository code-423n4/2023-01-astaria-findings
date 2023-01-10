AstariaRouter initialize function can be called by anybody and the caller would become the owner of the contract. Make sure that the call to initialize is done in the same transaction as the one creating the contract.
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L83
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AuthInitializable.sol#L44


