#### Repeated Storage pointer declaration and initialize
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L608
There are through the contract several repeated  declaration and initialization of the same storage pointer in the function and in modifier called by the same function.
I report as example functions `makePayment` which calls `validateSTack` modifier.
Both function declare and initialize storage pointer `LienStorage storage s = _loadLienStorageSlot();`
By changing function `makePayment` as follows, it saves **1486** gas when calling `testBasicPublicVaultLoan()`

    function makePayment(
    uint256 collateralId,
    Stack[] calldata stack,
    uint8 position,
    uint256 amount)
    external
    //validateStack(collateralId, stack)
    returns (Stack[] memory newStack) {
    LienStorage storage s = _loadLienStorageSlot();
    bytes32 stateHash = s.collateralStateHash[collateralId];
    if (stateHash == bytes32(0) && stack.length != 0) {
      revert InvalidState(InvalidStates.EMPTY_STATE);  }
    if (stateHash != bytes32(0) && keccak256(abi.encode(stack)) != stateHash) {
      revert InvalidState(InvalidStates.INVALID_HASH);
    }
    (newStack, ) = _payment(s, stack, position, amount, msg.sender);
    _updateCollateralStateHash(s, collateralId, newStack); }


#### Validate commitment at earlier stage
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/AstariaRouter.sol#L511
Function commitToLiens(IAstariaRouter.Commitment[] memory commitments) is called for depositing collateral and requesting loans for multiple NFTs in one go.
Input parameter is an array of `IAstariaRouter.Commitment[]`. The input is validated by function `_validateCommitment` at end of a long function calls chain:
**IAstariaRouter.commitToLiens() -> _executeCommitment() -> IVaultImplementation.commitToLien ->_requestLienAndIssuePayout -> _validateCommitment**
The validation of commitment can be pushed up to `IAstariaRouter.commitToLiens` since `_validateCommitment` parameters are already known at this stage: `IAstariaRouter.Commitment calldata params, address receiver`
This would save a lot of gas in case validation of commitment fails.


#### Decalaration of currentWithdrawProxy in the same function
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L366 AND L397
`address currentWithdrawProxy = s.epochData[s.currentEpoch - 1].withdrawProxy;` is declared twice in the same function.
By removing second declaration there's a save of **1169** gas for calling function `transferWithdrawReserve()` using `testWithdrawLiquidatedNoBids()`

#### Remove unused local variable.
Remove local variables declared and not used in the function to save gas upon deploying and function calls
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/src/ClearingHouse.sol#L139 - `ILienToken.AuctionStack[] storage stack = s.auctionStack.stack;` 
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L632 - `uint256 end = stack[position].end;` 
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L138 `address tokenContract = underlying.tokenContract;`
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L139 `uint256 tokenId = underlying.tokenId;`
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L342 `address tokenContract = underlying.tokenContract;`
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/PublicVault.sol#L262 `uint256 assets = totalAssets();`

#### Check "if (amount > owed) amount = owed;" is not needed because repeated in the same function
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L814
At above mentioned line it's checked that `if (amount > owed)` and if **TRUE** `amount = owed;`
At line 826 `stack.point.amount = owed.safeCastTo88();`
`amount` variable is not used until L829  where there's the if statement `if (stack.point.amount > amount)` which has the same result of `if (owed.safeCastTo88() > amount)`.
If this check is **FALSE** `amount = stack.point.amount;` which is equivalent to `amount = owed;`
Therefore one statement between the two in L814 or L836 is redundant

