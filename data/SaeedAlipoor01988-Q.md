•	In the CollateralToken contract and function _file, we get incoming as input. We will extract two variables from the incoming param. What and data. Based on the type of what we will use data and make changes in contract variables. The problem is that we don’t verify and make validation on data before making any changes to storage variables. It is more logical to first check the correctness of the data and then apply the changes. 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L214

For example, in the first block, we make changes on ASTARIA_ROUTER, what If a zero address is entered by mistake? 

•	In PublicVault contract and function _redeemFutureEpoch, we use below code, 

      if (allowed != type(uint256).max) {
        es.allowance[owner][msg.sender] = allowed - shares;
      }

How we are sure that shares is not more than allowed?

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L163

•	In the WithdrawProxy contract and claim function we don’t check balance is more than s.expected or is equal to s.expected !. what if the balance is equal to s.expected? 

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L264

•	In the WithdrawProxy contract and claim function, we are using the below check block :

    if (block.timestamp < s.finalAuctionEnd) {
      revert InvalidState(InvalidStates.FINAL_AUCTION_NOT_OVER);
    }

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L250

s.finalAuctionEnd is uint40, so I think we need to convert block.timestamp to uint40 and then make the comparison. For example use block.timestamp.safeCastTo40().

•	In the ClearingHouse contract and function balanceOfBatch, we are using for loop based on two arrays, we need to first be the sure length of the two arrays is the same.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/ClearingHouse.sol#L96

•	0 address check
0 address control should be done in these parts;

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339

•	Omissions in Events
Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters. The events should include the new value and old value where possible.

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L339

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L273

https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L359