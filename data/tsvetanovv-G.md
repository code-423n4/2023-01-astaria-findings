## [GAS-01] USE NAMED RETURNS FOR LOCAL VARIABLES WHERE IT IS POSSIBLE
```
Vault.sol:
62: returns (uint256)
47: function COLLATERAL_ID() public pure returns (uint256) {
51: function IMPL_TYPE() public pure returns (uint8) {
78: function supportsInterface(bytes4 interfaceId) external view returns (bool) { 
85: returns (uint256)
193: ) external override returns (bytes4) {

WithdrawProxy.sol:
202: returns (bool)

CollateralToken.sol:
189: returns (bool)

```

***

## [GAS-02] `x = x - y` costs less gas than `x -= y`, same for addition

```
WithdrawProxy.sol:
277: 	balance -= transferAmount;
305: 	s.expected += newLienExpectedValue.safeCastTo88();

PublicVault.sol:
174: es.balanceOf[owner] -= shares;
179: es.balanceOf[address(this)] += shares;
405: s.withdrawReserve -= drainBalance.safeCastTo88();
583: s.yIntercept += assets.safeCastTo88();
606: s.strategistUnclaimedShares += feeInShares;
624: s.yIntercept += params.increaseYIntercept.safeCastTo88();

AstariaRouter.sol:
514: totalBorrowed += payout;

LienToken.sol:
160: potentialDebt += _getOwed(
473: potentialDebt += _getOwed(newStack[j], newStack[j].point.end);
517: totalSpent += spent;
518: payment -= spent;
661: totalCapitalAvailable -= spent;
704: maxPotentialDebt += _getOwed(stack[i], stack[i].point.end);

ERC20-Cloned.sol:
54: s.balanceOf[msg.sender] -= amount;
59: s.balanceOf[to] += amount;
93: s.balanceOf[from] -= amount;
98: s.balanceOf[to] += amount;
174: s._totalSupply += amount;
179: s.balanceOf[to] += amount;
187: s.balanceOf[from] -= amount;
192: s._totalSupply -= amount;

VaultImplementation.sol:
404: amount -= fee;
```

You can replace all `-=` and `+=` occurrences to save gas

***

## [GAS-03] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()

Use abi.encodePacked() where possible to save gas.

`CollateralToken.sol:`
```
520: abi.encode(listingOrder.parameters)
```

`LienToken.sol:`
```
229:   abi.encode(newStack)
271:   if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
399:   abi.encode(newStack)
441:   newLienId = uint256(keccak256(abi.encode(params.lien)));
550:   lienId = uint256(keccak256(abi.encode(lien)));
680:   s.collateralStateHash[collateralId] = keccak256(abi.encode(stack));
```
***

## [GAS-04] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.

Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct
***

## [GAS-05] SPLITTING REQUIRE() STATEMENTS THAT USE `&&` SAVES GAS
```
PublicVault.sol:
672:    require(
673:      currentEpoch != 0 &&
674:        msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
675:    );

687:    require(
688:      currentEpoch != 0 &&
689:        msg.sender == s.epochData[currentEpoch - 1].withdrawProxy
690:    );

ERC20-Cloned.sol:
144: recoveredAddress != address(0) && recoveredAddress == owner,
```
***
