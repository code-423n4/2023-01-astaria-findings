# SOLIDITY GAS OPTIMIZATION REPORT ⛽

## [G-1] Use shift right/left instead of division/multiplication if possible.

### Description
A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left.
While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas.
urthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

### ✅ Recommendation
You should change multiplication and division with powers of two to bit shift.

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/AstariaRouter.sol#L112|[s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L112 )|
|2023-01-astaria/src/AstariaRouter.sol#L115|[s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L115 )|
---

## [G-2] Using storage instead of memory for structs/arrays saves gas.

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.
Instead of declaring the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct ([src](https://ethereum.stackexchange.com/questions/128380/why-using-storage-keyword-instead-of-memory-cost-less-gas))

### ✅ Recommendation
Use storage for `struct/array`

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/AstariaRouter.sol#L527|[address[] memory allowList = new address[](1);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L527 )|
|2023-01-astaria/src/ClearingHouse.sol#L200|[Order[] memory listings = new Order[](1);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L200 )|
|2023-01-astaria/src/CollateralToken.sol#L432|[OfferItem[] memory offer = new OfferItem[](1);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L432 )|
|2023-01-astaria/src/CollateralToken.sol#L444|[ConsiderationItem[] memory considerationItems = new ConsiderationItem[](2);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L444 )|
|2023-01-astaria/src/CollateralToken.sol#L484|[uint256[] memory prices = new uint256[](2);](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L484 )|
---

## [G-3] Empty blocks should be removed or emit something.

### Description
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified
			```solidity
if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}
			```

### ✅ Recommendation
Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/BeaconProxy.sol#L87|[function _beforeFallback() internal virtual {}](https://github.com/code-423n4/2023-01-astaria/blob/main/src/BeaconProxy.sol#L87 )|
|2023-01-astaria/src/ClearingHouse.sol#L104|[function setApprovalForAll(address operator, bool approved) external {}](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L104 )|
|2023-01-astaria/src/ClearingHouse.sol#L186|[) public {}](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L186 )|
|2023-01-astaria/src/VaultImplementation.sol#L272|[) internal virtual {}](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L272 )|
|2023-01-astaria/src/VaultImplementation.sol#L277|[{}](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L277 )|
---

## [G-4] Use two `require` statements instead of operator `&&` to save gas.

### Description
Usage of double require will save you around 10 gas with the optimizer enabled.
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.
 Example:
```Solidity
contract Requires {
uint256 public gas;
			
				function check1(uint x) public {
					gas = gasleft();
					require(x == 0 && x < 1 ); // gas cost 22156
					gas -= gasleft();
				}
			
				function check2(uint x) public {
					gas = gasleft();
					require(x == 0); // gas cost 22148
					require(x < 1);
					gas -= gasleft();
	}
}
```


### ✅ Recommendation
Consider changing one `require()` statement to two `require()` to save gas

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/Vault.sol#L65|[require(s.allowList[msg.sender] && receiver == owner());](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65 )|
---

## [G-5] `abi.encode()` is less efficient than `abi.encodePacked()`

### Description
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))` 
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

### ✅ Recommendation
Use `abi.encodePacked()` where possible to save gas

### 🔍 Findings:
| | |
|---|---|
|2023-01-astaria/src/CollateralToken.sol#L124|[s.collateralIdToAuction[collateralId] != keccak256(abi.encode(params))](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L124 )|
|2023-01-astaria/src/CollateralToken.sol#L520|[abi.encode(listingOrder.parameters)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L520 )|
|2023-01-astaria/src/LienToken.sol#L229|[abi.encode(newStack)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L229 )|
|2023-01-astaria/src/LienToken.sol#L271|[if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L271 )|
|2023-01-astaria/src/LienToken.sol#L406|[abi.encode(newStack)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L406 )|
|2023-01-astaria/src/LienToken.sol#L448|[newLienId = uint256(keccak256(abi.encode(params.lien)));](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L448 )|
|2023-01-astaria/src/LienToken.sol#L563|[lienId = uint256(keccak256(abi.encode(lien)));](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L563 )|
|2023-01-astaria/src/LienToken.sol#L698|[s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L698 )|
|2023-01-astaria/src/VaultImplementation.sol#L155|[abi.encode(](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L155 )|
|2023-01-astaria/src/VaultImplementation.sol#L184|[abi.encode(STRATEGY_TYPEHASH, s.strategistNonce, strategy.deadline, root)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L184 )|
---
