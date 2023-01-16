## [G-01] Using bools for storage incurs overhead

### Description

Use uint256 for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

### Findings

- [src/interfaces/ILienToken.sol#L49)](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/ILienToken.sol#L49) => `bool atLiquidation;`
- [src/interfaces/IVaultImplementation.sol#L38](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L38) => `bool allowListEnabled;`
- [src/interfaces/IVaultImplementation.sol#L46](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L46) => `bool allowListEnabled;`
- [src/interfaces/IVaultImplementation.sol#L47](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IVaultImplementation.sol#L47) => `bool isShutdown;`
- [src/LienToken.sol#L810](https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol#L810) => `bool isPublicVault = _isPublicVault(s, lienOwner);`
- [src/VaultImplementation.sol#L399](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L399) => `bool feeOn = feeTo != address(0);`

### References

- [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)

## [G-02] Postfix increment/decrease used

### Description

The prefix increment / decrease expression returns the updated value after it's incremented while the postfix increment / decrease expression returns the original value.

Be careful when employing this optimization anytime the return value of the expression is utilized later; for instance, `uint a = i++` and `uint a = ++i` produce different values for `a`.

### Findings

```Solidity
https://github.com/AstariaXYZ/astaria-gpl/blob/4b49fe993d9b807fe68b3421ee7f2fe91267c9ef/src/ERC721.sol
::136 =>       s._balanceOf[from]--;
::223 =>       s._balanceOf[owner]--;

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol
::539 =>     s.epochData[epoch].liensOpenForEpoch--;
```

### References

- [Why does ++i cost less gas than i++?](https://ethereum.stackexchange.com/questions/133161/why-does-i-cost-less-gas-than-i#answer-133164)
- [Gas Optimizations for the Rest of Us](https://m1guelpf.blog/d0gBiaUn48Odg8G2rhs3xLIjaL8MfrWReFkjg8TmDoM)


## [GAS-03] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

### Description

Gas consumption can be greater if you use items that are less than 32 bytes in size. This is such that the EVM can only handle 32 bytes at once. In order to increase the element's size from 32 bytes to the necessary amount, the EVM must do extra operations if it is lower than that. When necessary, it is advised to utilize a bigger size and then downcast.

### Findings

```Solidity
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol
::329 =>       (uint8 TYPE, address addr) = abi.decode(data, (uint8, address));
::368 =>         (uint8 implType, address addr) = abi.decode(data, (uint8, address));
::442 =>     uint8 nlrType = uint8(_sliceUint(commitment.lienRequest.nlrDetails, 0));

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol
::81 =>     mapping(uint8 => address) strategyValidators;
::82 =>     mapping(uint8 => address) implementations;

https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IPublicVault.sol
::33 =>     mapping(uint64 => EpochData) epochData;

https://github.com/code-423n4/2023-01-astaria/blob/main/src/LienToken.sol
::309 =>       uint88 owed = _getOwed(stack[i], block.timestamp);
::803 =>     uint64 end = stack.point.end;

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol
::449 =>       uint48 newSlope = s.slope + lienSlope.safeCastTo48();
::453 =>     uint64 epoch = getLienEpoch(lienEnd);
::523 =>       uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
::605 =>       uint88 feeInShares = convertToShares(fee).safeCastTo88();
::622 =>       uint48 newSlope = s.slope - params.lienSlope.safeCastTo48();
::654 =>     uint64 lienEpoch = getLienEpoch(params.lienEnd);
::671 =>     uint64 currentEpoch = s.currentEpoch;
::686 =>     uint64 currentEpoch = s.currentEpoch;

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol
::314 =>       uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();
```

### References

- [Layout of State Variables in Storage | Solidity docs](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html#layout-of-state-variables-in-storage)
- [GAS OPTIMIZATIONS ISSUES by Gokberk Gulgun](https://hackmd.io/@W1m6lTsFT5WAy9C_lRTX_g/rkr5Laoys)