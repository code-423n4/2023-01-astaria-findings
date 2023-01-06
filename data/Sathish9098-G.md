##

## [GAS-1]  Instead of using operator && on single require  . Using double REQUIRE  checks can save more gas. After splitting require statement its possible same 8 gas

BEFORE MIGRATION : 

require(s.allowList[msg.sender] && receiver == owner());

Recommended Mitigation Steps

require(s.allowList[msg.sender] );
require(s.allowList[receiver == owner());

[FILE: 2023-01-astaria/src/Vault.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol)

         65:  require(s.allowList[msg.sender] && receiver == owner());

##

## [GAS-2] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

[FILE: 2023-01-astaria/src/ClearingHouse.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol)

       200 : Order[] memory listings = new Order[](1);

##

## [GAS-3]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

     function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    returns (uint256 assets)
    {
    require(msg.sender == VAULT(), "only vault can mint");
    _mint(receiver, shares);
    return shares;
    }

   function withdraw(
    uint256 assets,
    address receiver,
    address owner
  )
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyWhenNoActiveAuction
    returns (uint256 shares)
  {
    return super.withdraw(assets, receiver, owner);
  }

  function redeem(
    uint256 shares,
    address receiver,
    address owner
  )
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyWhenNoActiveAuction
    returns (uint256 assets)
   {
    return super.redeem(shares, receiver, owner);
   }

##

## [GAS-4]  USE FUNCTION INSTEAD OF MODIFIERS

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

     modifier onlyWhenNoActiveAuction() {
    WPStorage storage s = _loadSlot();
    // If auction funds have been collected to the WithdrawProxy
    // but the PublicVault hasn't claimed its share, too much money will be sent to LPs
    if (s.finalAuctionEnd != 0) {
      // if finalAuctionEnd is 0, no auctions were added
      revert InvalidState(InvalidStates.NOT_CLAIMED);
     }
     _;
    }      

##

## [GAS-5] Use uint40 Can Increase Gas Cost . Uint256 uses less gas than uint8 in loops and other situations.

A smart contract's gas consumption can be higher if developers use items that are less than 32 bytes in size because the Ethereum Virtual Machine can only handle 32 bytes at a time. In order to increase the element's size to the necessary size, the EVM has to perform additional operations

such as uint40, are only more effective when multiple variables can be stored in the same storage space, like in structs. 

[FILE: 2023-01-astaria/src/WithdrawProxy.sol](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol)

       314:   uint40 auctionEnd = (block.timestamp + finalAuctionDelta).safeCastTo40();  





GAS-1	Use assembly to check for address(0)	43
GAS-2	Using bools for storage incurs overhead	4
GAS-3	Cache array length outside of loop	12
GAS-4	Use calldata instead of memory for function arguments that do not get mutated	21
GAS-5	Use Custom Errors	15
GAS-6	Don't initialize variables with default value	3
GAS-7	Functions guaranteed to revert when called by normal users can be marked payable	4
GAS-8	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	5
GAS-9	Using private rather than public for constants, saves gas	1
GAS-10	Use != 0 instead of > 0 for unsigned integer comparison	17
GAS-11	internal functions not called by the contract should be removed	1