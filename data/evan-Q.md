### Low 1: Setting feeTo without setting protocolFeeDenominator causes DOS for all vaults (no one can borrow from them anymore)
https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L379-L410
https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L650-L655
Setting feeTo will trigger ROUTER().getProtocolFee(amount) in handleProtocolFee which gets called in every successful commitToLien.
This will trigger a division by 0 if protocolFeeDenominator is not set or set to 0.

### Low 2: Unnecessary payable functions
Mint, withdraw, deposit, redeem functions in astariaRouter are all payable but msg.value doesn't seem to be used anywhere (even in the super.{mint, withdraw, deposit, redeem}).

### Low 3: getAmountOwingAtLiquidation is incorrect since s.auctionData[stack.lien.collateralId].stack is never written to
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L550
In LienToken.sol, the only line that updates s.auctionData updates the liquidator.
https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L302



