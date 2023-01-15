1. Incase on repetitive require(msg.sender == ...) in multiple contracts checks you can use single function to save gas:

function _ownerCheck(address param) internal {
  require(msg.sender == param)
}

Used in Vault.sol (line 71), PublicVault.sol(line 508), AstariaRouter.sol (lines: 341, 347, 354, 361), ClearingHouse.sol (lines: 72, 199, 216, 223)


2. ClearingHouse.sol

multiple lines

Public functions not called by the contract should be declared external instead;

3. PublicVault.sol

line 262 

Not used varaible - uint256 assets = totalAssets();

line 575

Unused function param - uint256 shares

4. WithdrawProxy.sol

multiple lines

Public functions not called by the contract should be declared external instead;

WPStorage storage s = _loadSlot() - should also be a modifier - used in multiple lines;

5. AtariaRouter.sol

A loo-a-like functions like BEACON_PROXY_IMPLEMENTATION(), LIEN_TOKEN(), TRANSFER_PROXY(), COLLATERAL_TOKEN() should be united in a single that return a struct.


RouterStorage storage s = _loadRouterSlot() - on lines 346, 353, 360 should be made as modifier to save gas.

line 611 

function canLiquidate() - second parametr in return could never be checked if first one returns false.

6. Emit functions should have indexed fields.

7. LienToken.sol 

line632 

Unused variable uint256 end = stack[position].end;

line 780

Unused variable uint256 delta_t = stack.point.end - block.timestamp;

line 262

Unused variable uint256 assets = totalAssets();

8. 

LienToken.sol

line 459 

function _appendStack() return nothing.

9. Lack of natspec documentation for important functions like withdraw, deposit, mint, etc.