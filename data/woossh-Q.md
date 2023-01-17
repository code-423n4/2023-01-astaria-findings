WithdrawProxy.sol:
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/WithdrawProxy.sol#L240

The claim() function in the WithdrawProxy.sol smart contract is used to claim the share of a deposit. The claim can be done only once the final auction is over.
However, the claim() function does not verifiy whether the claim has been done already. As such, it is possible for an attacker to claim an unlimited amount of time.



CollateralToken.sol: 
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L487
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L469
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L516

The _generateValidOrderParameters() function does not validate the maxDuration param. That value is used in the function to calculate the endTime.
As the maxDuration param is an uint256, an attacker can enter a large number which will set the endtime to an unexpected value.

The _generateValidOrderParameters() is internal but is called by the public function auctionVault() which will also use the unexpected maxDuration value through the _listUnderlyingOnSeaport() function.