## Gas Optimization

| G-O    |Issue|
|:------:|:----|
| [G&#x2011;01] | THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES | 2 |
| [G&#x2011;02] | Remove public visibility from constant variables | 6 |
| [G&#x2011;03] | Use uint256 instead of uint8 for storage variables and function parameters | 11 |
| [G&#x2011;04] | Use custom errors instead of revert strings | 19 |
| [G&#x2011;05] | Use x != 0 instead of x > 0 for uint types | 31 |
| [G&#x2011;06] | x = x - y costs less gas than x -= y, same for addition | 2 |
| [G&#x2011;07] | USE CALLDATA INSTEAD OF MEMORY | 1 |
| [G&#x2011;08] | Splitting require() statements that use && saves gas | 1 |
| [G&#x2011;09] | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3 | 1 |
| [G&#x2011;10] | Caching the array length is more gas efficient | 1 |

Total: 10 issues

### [G-01] THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES

If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

File: `LienToken.sol`

```solidity
uint256 potentialDebt = 0; 
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L152


### [G-02] Remove public visibility from constant variables

Constant variables are custom to the contract and won't need to be read on-chain - anyone can just see their values from the source code and, if needed, hardcode them into other contracts. Removing the public visibility will optimise deployment cost since no automatically generated getters will exist in the bytecode of the contract. 

File: `VaultImplementation.sol`

```solidity
bytes32 public constant STRATEGY_TYPEHASH
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L44

### [G-03] Use uint256 instead of uint8 for storage variables and function parameters

Unless you are doing variable-packing in storage it is best to use always use uint256 because all other uint types get automatically padded to uint256 in memory anyway. Same applies for function parameters

File: `VaultImplementation.sol`

```solidity
uint8 position
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/VaultImplementation.sol#L315

### [G-04] Use custom errors instead of revert strings

Solidity 0.8.4 added the custom errors functionality, which can be use instead of revert strings, resulting in big gas savings on errors. 
Replace all revert statements in the contracts with custom error ones.

File: `ClearingHouse.sol`

Example:

```solidity
require(payment >= currentOfferPrice, "not enough funds received");
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L134

### [G-05] Use x != 0 instead of x > 0 for uint types

The != operator costs less gas than > and for uint types you can use it to check for non-zero values to save gas

File: `ClearingHouse.sol`

```solidity
if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L160

### [G-06] x = x - y costs less gas than x -= y, same for addition

You can replace all -= and += occurrences to save gas 

Files: `AstariaRouter.sol`

```solidity
totalBorrowed += payout;
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L512



Files: `LienToken.sol`

```solidity
 potentialDebt += _getOwed(
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L160

### [G-07] USE CALLDATA INSTEAD OF MEMORY

Some methods are declared as external but the arguments are defined as memory instead of as calldata.
By marking the function as external it is possible to use calldata in the arguments shown below and save significant gas.

File: `ClearingHouse.sol`

```solidity
function validateOrder(Order memory order) external 
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L197

### [G-08] Splitting require() statements that use && saves gas

See this issue which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas 

File: `Vault.sol`

```solidity
require(s.allowList[msg.sender] && receiver == owner());
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/Vault.sol#L65

### [G-09] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

File: `ClearingHouse.sol`

```solidity
Order[] memory listings = new Order[](1);
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L200



File: `LienToken.sol`

```solidity
Stack memory newStackSlot;
```

```solidity
Point memory point = Point({
```

Lines of code:

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L401

- https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L449]


### [G-10] Caching the array length is more gas efficient.

This is because access to a local variable in solidity is more efficient than query storage / calldata / memory

We recommend to change from:

for (uint256 i=0; i<array.length; i++) { ... }
to:
uint len = array.length
for (uint256 i=0; i<len; i++) { ... }

This issue persist all through the contracts.