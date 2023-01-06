Use != 0 instead of > 0 for Unsigned Integer Comparison

astaria/src/WithdrawProxy.sol::280 => if (balance > 0)
astaria/src/PublicVault.sol::277 => if (timeToEpochEnd() > 0) 
astaria/src/PublicVault.sol::282 => if (s.withdrawReserve > 0) 
astaria/src/PublicVault.sol::303 => if (s.epochData[s.currentEpoch].liensOpenForEpoch > 0) 
astaria/src/PublicVault.sol::393 => s.withdrawReserve > 0 
astaria/src/PublicVault.sol::636 => if (params.remaining > 0)
astaria/src/AstariaRouter.sol::476 => if (timeToSecondEpochEnd > 0 && details.duration > timeToSecondEpochEnd)