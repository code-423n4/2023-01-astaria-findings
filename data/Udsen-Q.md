## 1. NATSPEC IS MISSING

NatSpec is missing for the following functions , constructor and modifier.
Use NatSpec for better understanding of the code.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L14-L232
https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L14-L346
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L216-L244
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L198-L318

## 2. USE NAMED RETURN VALUES FOR BETTER READABILITY AND UNDERSTANDING OF THE CODE

    returns (ClearingHouseStorage storage s)  
    {
      uint256 slot = CLEARING_HOUSE_STORAGE_SLOT;
      assembly {
      s.slot := slot
    }
		
above code can be changed as follows for better readability and understanding

    returns (ClearingHouseStorage storage) 
    {
       ClearingHouseStorage storage s;
       uint256 slot = CLEARING_HOUSE_STORAGE_SLOT;
       assembly {
       s.slot := slot
       }
	return s;
     }		

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L58-L64

There are 3 more instances of this issue:

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L66-L76
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L154
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L82-L88

## 3. UNUSED FUNCTION ARGUMENTS CAN BE COMMENTED OR REMOVED.

    function balanceOf(address account, uint256 id)
      external
      view
      returns (uint256)
    {
      return type(uint256).max;
    }
  
Two passed in function parameters above are not used in the implementation and hence can be commented as below:

    function balanceOf(address /*account*/, uint256 /*id*/)
      external
      view
      returns (uint256)
    {
      return type(uint256).max;
    }
  
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L88

There are 3 more instance of this issue:

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-L102
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L106-L112
https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L147

## 4. USE `events` TO LOG BUSINESS CRITICAL OPERATIONS.

`events` have not been used for business critical logics in the below code snippet.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L114-L167

## 5. SAME LINE OF CODE FOR A `require` STATEMENT IS REPEATING AND CAN BE CODED INTO A `modifier`.

    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()))
	
Above line of code is repeated in three places as follows:

https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L199
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L216
https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L223

repeating code line can be replace by a modifier as follows:

	modifier(IAstariaRouter ASTARIA_ROUTER){
		require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
		_;
	}
	
## 6. FROM `solidity v0.8.12` ONWARDS, IT IS POSSIBLE TO USE `string.concat(s1, s2)` TO CONCATENATE TWO STRINGS S1 AND S2.

Since the project is using solidity v0.8.17, it is possible to use `string.concat()` inplace of `abi.encodePacked()` to concatenate two strings.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L83
https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L93

## 7. MISSING `address(0)` CHECKS FOR THE FOLLOWING ADDRESS INPUTS TO THE IMPORTANT FUNCTIONS.

    s.delegate = delegate_;
	
By mistake delegate_ could be set to address(0). It is a good practice to check for address(0).	

	require(delegate_ != address(0), "Address cannot be zero");
	
Hence it is recommended to check for the address(0) for the delegate_ variable before `s.delegate = delegate_` is called.
	
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L213

## 8. USE OPENZEPPELIN `ECDSA.sol` LIBRARY FOR SIGNATURE VERIFICATION INSTEAD OF USING BUILT-IN `ecrecover()` FUNCTION WHICH IS SUSCEPTIBLE TO SIGNATURE MALLEABILITY.

    address recovered = ecrecover(
      keccak256(
        _encodeStrategyData(
          s,
          params.lienRequest.strategy,
          params.lienRequest.merkle.root
        )
      ),
      params.lienRequest.v,
      params.lienRequest.r,
      params.lienRequest.s
    ); 
	
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L246-L257

## 9. DIFFERENT RETURN VARIABLE IS DECLARED IN THE FUNCTION, BUT DIFFERENT VARIABLE IS ACTUALLY RETURNED.

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
  
Variable `assets` is expected to be returned as per the code `returns (uint256 assets)`. But actually variable `shares` is returned.

https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L132-L141