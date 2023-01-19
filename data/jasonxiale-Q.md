# AstariaRouter.isValidRefinance doesn't correspond to the document.
## According to https://docs.astaria.xyz/docs/protocol-mechanics/refinance, the code should look like 

    function isValidRefinance(
        ILienToken.Lien memory lien,
        LienDetails memory newLien
    ) external view returns (bool) {
        uint256 minNewRate = uint256(lien.rate) - minInterestBPS;
  
        return (newLien.rate >= minNewRate ||
            ((block.timestamp + newLien.duration - lien.start - lien.duration) >=
                minDurationIncrease));
    }

Considering current source enhance the limit, I think the document should be updated

# CollateralToken._listUnderlyingOnSeaport comment doesn't  correspond to the code.
## As https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L507 shows, 

    function _listUnderlyingOnSeaport(
        CollateralStorage storage s,
        uint256 collateralId,
        Order memory listingOrder
    ) internal {
        //get total Debt and ensure its being sold for more than that

        if (listingOrder.parameters.conduitKey != s.CONDUIT_KEY) {
            revert InvalidConduitKey();
        }


but there is no code related to **//get total Debt and ensure its being sold for more than that**

# LienToken.createLien comment doesn't correspond to the code.
## As https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L400

    function createLien(ILienToken.LienActionEncumber memory params)
        external
        requiresAuth
        validateStack(params.lien.collateralId, params.stack)
        returns (
            uint256 lienId,
            Stack[] memory newStack,
            uint256 lienSlope
        )
    {
        LienStorage storage s = _loadLienStorageSlot();
        //0 - 4 are valid
        Stack memory newStackSlot;
        (lienId, newStackSlot) = _createLien(s, params);
  
        newStack = _appendStack(s, params.stack, newStackSlot);
        s.collateralStateHash[params.lien.collateralId] = keccak256(
            abi.encode(newStack)
        );

the comment __//0 - 4 are valid__ should be add to https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/LienToken.sol#L464

    function _appendStack(
        LienStorage storage s,
        Stack[] memory stack,
        Stack memory newSlot
    ) internal returns (Stack[] memory newStack) {
        //0 - 4 are valid
        if (stack.length >= s.maxLiens) {
            revert InvalidState(InvalidStates.MAX_LIENS);
        }
