## [G-1] Structs can be closely packed
here is the permalink of it [IAstariaRouter.sol#L101-L105](https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/interfaces/IAstariaRouter.sol#L101-L105)
here `address vault;` can be placed under `uint8 version;` so that we can save 1 storage slot