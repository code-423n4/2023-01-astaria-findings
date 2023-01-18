(1) AstariaRouter.sol, Line 505-516
Because unit256 is initialized i will always be 0 because the increment section, i++ is placed at the end of the program i never increments.

Current code:
uint256 i;
    for (; i < commitments.length; ) {
      if (i != 0) {
        commitments[i].lienRequest.stack = stack;
      }
      uint256 payout;
      (lienIds[i], stack, payout) = _executeCommitment(s, commitments[i]);
      totalBorrowed += payout;
      unchecked {
        ++i;
      }

