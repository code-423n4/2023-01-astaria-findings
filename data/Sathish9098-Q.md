# LOW FINDINGS

## [L-1]  ids parameter not used any where inside the functions. Its better to be removed from functions parameter. 

### Impact:

Without any usage its cause a confusion while calling the functions.

### Proof Of Work: 

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

          function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)  //@Audit ids parameter not used inside the functions LOW FINDINGS
          external
          view
          returns (uint256[] memory output)
          {
          output = new uint256[](accounts.length);
          for (uint256 i; i < accounts.length; ) {
          output[i] = type(uint256).max;
          unchecked {
          ++i;
          }
          }
          }

### Recommended Mitigation Steps: 

If there is no idea for using  ids parameter it can be safely removed . 

# NON CRITICAL FINDINGS

## [NC - 1 ]  NATSPEC COMMENTS SHOULD BE ADDED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

Recommendation
NatSpec comments should be increased in Contracts

PROOF OF WORK:

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)

##

## [NC-2]  accounts parameter array length should be checked. Its possible to call balanceOfBatch() function with empty array.


[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

        function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)  
          view
          returns (uint256[] memory output)
          {
          output = new uint256[](accounts.length);
          for (uint256 i; i < accounts.length; ) {
          output[i] = type(uint256).max;
          unchecked {
          ++i;
          }
          }
          }
 
### Recommended Mitigation Steps: 

   require(accounts.length>0, " Array is empty");

##

## [NC-3] Shorter inheritance list

[FILE: 2023-01-astaria/src/CollateralToken.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol)


     contract CollateralToken is
     AuthInitializable,
     ERC721,
     IERC721Receiver,
     ICollateralToken,
     ZoneInterface

##

### [NC-4] TYPOS

[FILE : Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

///Audit  vautls  => vaults 

 90 :  //invalid action private vautls can only be the owner or strategist





NC-1	require() / revert() statements should have descriptive reason strings	32
NC-2	Return values of approve() not checked	1
NC-3	Event is missing indexed fields	30
NC-4	Constants should be defined rather than using magic numbers	19
NC-5	Functions not used internally could be marked external	83

L-1	Do not use deprecated library functions	6
L-2	Unsafe ERC20 operation(s)	2
L-3	Unspecific compiler version pragma	4