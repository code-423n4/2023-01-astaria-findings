
*Public functions not called by the contract should be declared external instead( not used in any other function in the contract):
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L 192,196,200,204,208, 212,216, 251,345, 461,534,552,562, 615, 684

*internal functions not called by the contract should be removed to save deployment gas:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L 271,413, 439, 575

*Functions guaranteed to revert when called by normal users can be marked payable:
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L 517,534, 562, 617,634,643,

*Splitting require() statements that use && saves gas:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L  673, 688, 

*Not using the named return variables when a function returns, wastes deployment gas:
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L  143,717

*Using bools for storage incurs overhead:
Use uint256(1) and uint256(2) for true/false instead
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L349

*Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead:
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher.
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L 71,103,224,605,440,449,459,523,529,622, 142,153,192,196,216,362,453,534,538,546,548,552,556,654,671,686
