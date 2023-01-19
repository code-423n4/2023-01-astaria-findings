1.

See AstariaRouter.sol

Change

s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
s.maxInterestRate = ((uint256(1e16) * 200) / (365 days)).safeCastTo88();

to

s.minInterestBPS = uint32(158548959); // (uint256(1e15) * 5) / (365 days)
s.maxInterestRate = (63419583967).safeCastTo88(); // ((uint256(1e16) * 200) / (365 days)

Save execution gas around 433 for every line

2.

See collateralToken.sol

Change

uint256(uint160(settlementToken))

to

uint256(settlementToken)

Save execution gas around 6