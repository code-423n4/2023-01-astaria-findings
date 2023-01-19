
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1  | Non allowed users are able to mint and deposit in PublicVault | Low |  1 |
| 2  | Wrong named return variable declared | NC |  1 |
| 3  | Use existing modifier instead of add additional `require` checks | NC | 1 |
| 4  | Duplicated `require()` checks should be refactored to a modifier  | NC |  12 |
| 5  | Named return variables not used anywhere in the functions | NC | 3 |

## Findings

### 1- Non allowed users are able to mint and deposit in PublicVault :

Both `mint` and `deposit` functions allow any user to mint new shares or deposit assets if they know and address in the allow list (which can be obtained from on-chain data) which should not be allowed as only the addresses in the `allowList` should be able to call the `mint` and `deposit` functions.

Even though the deposited assets or the minted shares goes to the receiver (who is in the allowed list), the impact of this is that a malicious user can keep calling the `deposit` function for example by sending some assets, the `deposit` function will call the `afterDeposit` hook which updates the `s.yIntercept` value, and so the `yIntercept` will keep changing in value which can impact the protocol, not to forget that there is a deposit cap that can be reached.

I submitted this as Low because the malicious user must lose money to perform this "attack", but it's still valid because in the protocol logic it's said that only addresses from the `allowList` should be able to call the `mint` and `deposit` functions.

### Risk : Low

### Proof of Concept
Instances occurs in the code below :

File: src/PublicVault.sol [Line 233-244](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L233-L244)
```
function mint(uint256 shares, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[receiver]);
    }
    return super.mint(shares, receiver);
  }
```

File: src/PublicVault.sol [Line 251-265](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L251-L265)
```
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

As you can see both functions can be called by any user because the only check is `require(s.allowList[receiver])` which can be bypassed by getting an allowed address from the on-chain storage, and in the contract logic it is intended that only addresses from the `allowList` should be able to call the `mint` and `deposit` functions.

### Mitigation

To avoid this isssue, the check on the `receiver` address should be replaced in both `mint` and `deposit` functions by `msg.sender` : 

```
function mint(uint256 shares, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[msg.sender]);
    }
    return super.mint(shares, receiver);
  }
```


```
  function deposit(uint256 amount, address receiver)
    public
    override(ERC4626Cloned)
    whenNotPaused
    returns (uint256)
  {
    VIData storage s = _loadVISlot();
    if (s.allowListEnabled) {
      require(s.allowList[msg.sender]);
    }

    uint256 assets = totalAssets();

    return super.deposit(amount, receiver);
  }
```

### 2- Wrong named return variable declared :

### Risk : NON CRITICAL

The `mint` function from the WithdrawProxy contract is supposed to return the minted shares but a wrong return variable `asset` is declared instead of `shares`.

File: src/WithdrawProxy.sol [Line 132-141](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L132-L141)
```
function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyVault
    returns (uint256 assets)
{
    require(msg.sender == VAULT(), "only vault can mint");
    _mint(receiver, shares);
    return shares;
}
```

### Mitigation

The `mint` function should be modified as follow :

```
function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    returns (uint256 shares)
{
    _mint(receiver, shares);
}
```

### 3- Use existing modifier instead of add additional `require` checks :

### Risk : NON CRITICAL

The `mint` function from the WithdrawProxy contract should use the existing `onlyVault` modifier instead of declaring a duplicate `require` check statement.

File: src/WithdrawProxy.sol [Line 132-141](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L132-L141)
```
function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    onlyVault
    returns (uint256 assets)
{
    require(msg.sender == VAULT(), "only vault can mint");
    _mint(receiver, shares);
    return shares;
}
```

### Mitigation

The `mint` function should be modified as follow :

```
function mint(uint256 shares, address receiver)
    public
    virtual
    override(ERC4626Cloned, IERC4626)
    returns (uint256 assets)
{
    _mint(receiver, shares);
    return shares;
}
```

### 4- Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

### Risk : NON CRITICAL

### Proof of Concept

There are 6 instances of this issue in `VaultImplementation.sol`:

```
File: src/VaultImplementation.sol

78      require(msg.sender == owner())
96      require(msg.sender == owner())
105     require(msg.sender == owner())
114     require(msg.sender == owner())
147     require(msg.sender == owner())
211     require(msg.sender == owner())
```

Those checks should be replaced by a `onlyOwner` modifier as follow :

```
modifier onlyOwner(){
    require(msg.sender == owner());
    _;
}
```

There are 3 instances of this issue in `AstariaRouter.sol`:

```
File: src/AstariaRouter.sol

341     require(msg.sender == s.guardian);
347     require(msg.sender == s.guardian);
361     require(msg.sender == address(s.guardian));
```

Those checks should be replaced by a `OnlyGuardien` modifier as follow :

```
modifier OnlyGuardien(){
    require(msg.sender == s.guardian);
    _;
}
```

There are 3 instances of this issue in `ClearingHouse.sol`:

```
File: src/ClearingHouse.sol

199     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
216     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
223     require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
```

Those checks should be replaced by a `OnlyCollateral` modifier as follow :

```
modifier OnlyCollateral(){
    IAstariaRouter ASTARIA_ROUTER = IAstariaRouter(_getArgAddress(0));
    require(msg.sender == address(ASTARIA_ROUTER.COLLATERAL_TOKEN()));
    _;
}
```

### 5- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

### Risk : NON CRITICAL

### Proof of Concept
Instances include:

File: src/AstariaRouter.sol [Line 721](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L721)
```
returns (address vaultAddr)
```

File: src/CollateralToken.sol [Line 162](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L162)
```
returns (bytes4 validOrderMagicValue)
```

File: src/CollateralToken.sol [Line 177](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L177)
```
returns (bytes4 validOrderMagicValue)
```

### Mitigation
Either use the named return variables inplace of the return statement or remove them.