## 1. Enable/disable functions can be merged into a toggle function

The following functions have almost the same functionality:

File: `VaultImplementation.sol` [Line 101-117](https://github.com/code-423n4/2023-01-astaria/blob/main/src/VaultImplementation.sol#L101-L117)

```solidity
101:  /**
102:   * @notice disable the allowList for the vault
103:   */
104:  function disableAllowList() external virtual {
105:    require(msg.sender == owner()); //owner is "strategist"
106:    _loadVISlot().allowListEnabled = false;
107:    emit AllowListEnabled(false);
108:  }
109:
110:  /**
111:   * @notice enable the allowList for the vault
112:   */
113:  function enableAllowList() external virtual {
114:    require(msg.sender == owner()); //owner is "strategist"
115:    _loadVISlot().allowListEnabled = true;
116:    emit AllowListEnabled(true);
117:  }
```

To save gas, consider merging the functionalities into a new function, `setAllowListEnabled()`:

```solidity
  /**
   * @notice enable/disable the allowList for the vault
   */
  function setAllowListEnabled(bool isEnabled) external virtual {
    require(msg.sender == owner()); //owner is "strategist"
    _loadVISlot().allowListEnabled = isEnabled;
    emit AllowListEnabled(isEnabled);
  }
```

## 2. Splitting `require()` statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per &&.

- File: `PublicVault.sol` [Line 672-675](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L672-L675)
- File: `PublicVault.sol` [Line 687-690](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L687-L690)
- File: `Vault.sol` [Line 65](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L65)

## 3. Unused local variable

File: `PublicVault.sol` [Line 251-265](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L251-L265)

```solidity
  function deposit(uint256 amount, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[receiver]);
    }

    uint256 assets = totalAssets();

    return super.deposit(amount, receiver);
  }
```

As seen above, `uint256 assets` is never used. As such, it may be removed to save gas.

## 4. Unchecked math saves gas

Use the `unchecked` keyword to avoid unnecessary arithmetic checks and save gas when an underflow/overflow will not happen.

The following code line could be moved into the `unchecked` block as it will not overflow:

File: `AstariaRouter.sol` [Line 512](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L512)

## 5. Unused parameters

In the following functions, the parameters are never used:

- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L82-L88
- https://github.com/code-423n4/2023-01-astaria/blob/main/src/ClearingHouse.sol#L90-L102
