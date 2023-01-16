•	In the CollateralToken contract and function _file, we get incoming as input. We will extract two variables from the incoming param. What and data. We will use data and make changes in contract variables based on the type of what. The problem is that we don’t verify and make validation on data before making any changes to storage variables. It is more logical to check the data's correctness and then apply the changes. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L214

For example, in the first block, we make changes on ASTARIA_ROUTER, what If a zero address is entered by mistake? 

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	In PublicVault contract and function _redeemFutureEpoch, we use below code, 

      if (allowed != type(uint256).max) {
        es.allowance[owner][msg.sender] = allowed - shares;
      }

How we are sure that shares is not more than allowed?

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L163

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	In the WithdrawProxy contract and claim function we don’t check balance is more than s.expected or is equal to s.expected !. what if the balance is equal to s.expected? 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L264

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	In the WithdrawProxy contract and claim function, we are using the below check block :

    if (block.timestamp < s.finalAuctionEnd) {
      revert InvalidState(InvalidStates.FINAL_AUCTION_NOT_OVER);
    }

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L250

s.finalAuctionEnd is uint40, so I think we need to convert block.timestamp to uint40 and then make the comparison. For example use block.timestamp.safeCastTo40().

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	In the ClearingHouse contract and function balanceOfBatch, we are using for loop based on two arrays, 

address[] calldata accounts
uint256[] calldata ids

but we just used the accounts array and did not use the ids array.

  function balanceOfBatch(address[] calldata accounts, uint256[] calldata ids)
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


https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L90

The same is happening in the below function :
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L82

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	0 address check
0 address control should be done in these parts;

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339

////////////////////////////////////////////// ***** //////////////////////////////////////////////

•	Omissions in Events
Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters. The events should include the new value and old value where possible.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339

httpccs://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L273

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L359

////////////////////////////////////////////// ***** //////////////////////////////////////////////

in the below functions, based on the name of the functions, we are returning the balance of accounts. but in the body of functions we are returning type(uint256).max; 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L82

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L90

////////////////////////////////////////////// ***** //////////////////////////////////////////////

in the _execute function we accept  uint256 encodedMetaData as input to retrieve the token address from the encoded data.

but attackers can create any ERC20 token contract and use it on this function.

    address paymentToken = bytes32(encodedMetaData).fromLast20Bytes();

    uint256 payment = ERC20(paymentToken).balanceOf(address(this));

   ERC20(paymentToken).safeTransfer(
      s.auctionStack.liquidator,
      liquidatorPayment
    );

   ERC20(paymentToken).safeApprove(
      address(ASTARIA_ROUTER.TRANSFER_PROXY()),
      payment - liquidatorPayment
    );


    if (ERC20(paymentToken).balanceOf(address(this)) > 0) {
      ERC20(paymentToken).safeTransfer(
        ASTARIA_ROUTER.COLLATERAL_TOKEN().ownerOf(collateralId),
        ERC20(paymentToken).balanceOf(address(this))
      );
    }

attacker can pass all above steps by any created ERC20 token contract and run execute.

I advise checking that collateralId at the below line
 
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L136

matches and is related to the payment token address at the below line.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L123
