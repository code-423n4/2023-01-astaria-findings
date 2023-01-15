## 1. Use `string.concat()` to concatenate strings

As of Solidity v0.8.12 the `string.concat(a,b)` function is the preferred method, due to it being much more readable than `abi.encodePacked()`.

- File: `WithdrawProxy.sol` [Line 110](https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/WithdrawProxy.sol#L110)
- File: `WithdrawProxy.sol` [Line 124](https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/WithdrawProxy.sol#L124)
- File: `PublicVault.sol` [Line 83](https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L83)
- File: `PublicVault.sol` [Line 93](https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/PublicVault.sol#L93)

## 2. Deprecated pragma

> The pragma `pragma experimental ABIEncoderV2;` is still valid, but it is deprecated and has no effect. If you want to be explicit, please use `pragma abicoder v2;` instead.

Source: [docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html#silent-changes-of-the-semantics](https://docs.soliditylang.org/en/v0.8.13/080-breaking-changes.html#silent-changes-of-the-semantics):

- File; `CollateralToken.sol` [Line 16](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L16)
- File: `LienToken.sol` [Line 16](https://github.com/code-423n4/2023-01-astaria/blob/57c2fe33c1d57bc2814bfd23592417fc4d5bf7de/src/LienToken.sol#L16)

## 3. Solmate's `safeTransferFrom()` doesn't check for contract existence.

> `/// @dev Note that none of the functions in this library check that a token has code at all! That responsibility is delegated to the caller.`

Source: `SafeTransferLib.sol` [Line 9](https://github.com/transmissions11/solmate/blob/main/src/utils/SafeTransferLib.sol#L9)

As a result, `safeTransferFrom()` will return success even if the contract is non-existent. Due to this a contract may think that funds have transferred successfully when in reality no funds were transferred.

Here are some instances of this issue:

File: `Vault.sol` [Line 66](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L66), [Line 72](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L72)

```solidity
66:    ERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);

72:    ERC20(asset()).safeTransferFrom(address(this), msg.sender, amount);
```

File: `TransferProxy.sol` [Line 34](https://github.com/code-423n4/2023-01-astaria/blob/main/src/TransferProxy.sol#L34)

```solidity
34:    ERC20(token).safeTransferFrom(from, to, amount);
```

## 4. Use delete to clear variables instead of zero assignment

Using the [`delete`](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) keyword more clearly states your intention.

For example:

File: `WithdrawProxy.sol` [Line 284](https://github.com/code-423n4/2023-01-astaria/blob/main/src/WithdrawProxy.sol#L284)

```solidity
    s.finalAuctionEnd = 0;
```

The instance above could be changed to:

```solidity
    delete s.finalAuctionEnd;
```

## 5. Typo mistakes

It is recommended to use the correct spelling of words. Not doing so can decrease readability.

File: `Vault.sol` [Line 90](https://github.com/code-423n4/2023-01-astaria/blob/main/src/Vault.sol#L90)

```
       /// @audit vautls --> vaults
90:    //invalid action private vautls can only be the owner or strategist
```

File `CollateralToken.sol` ([Line 126](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L126), [Line 507](https://github.com/code-423n4/2023-01-astaria/blob/main/src/CollateralToken.sol#L507))

```
          /// @audit dont -> don't
126:      //revert auction params dont match

          /// @audit its -> (it's || it is)
507:      //get total Debt and ensure its being sold for more than that
```

File: `PublicVault.sol` ([Line 307](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L307), [Line 374](https://github.com/code-423n4/2023-01-astaria/blob/main/src/PublicVault.sol#L374))

```
        /// @audit calcualtion -> calculation
307:    // reset liquidationWithdrawRatio to prepare for re calcualtion

        @audit then -> than
374:    // prevent transfer of more assets then are available
```

File: `IERC1155Receiver.sol` [Line 36](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IERC1155Receiver.sol#L36)

```
      /// @audit "a multiple" -> "multiple"
36:   * @dev Handles the receipt of a multiple ERC1155 token types. This function
```

## 6. `safeCastTo32()` not being utilized

`SafeCastLib.sol` is imported in `AstariaRouter.sol`. However, it is not fully utilized.

For instance:

File: `AstariaRouter.sol` [Line 112](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L112)

```solidity
    s.minInterestBPS = uint32((uint256(1e15) * 5) / (365 days));
```

Instead of the above, consider utilizing `safeCastTo32()`:

```solidity
    s.minInterestBPS = ((uint256(1e15) * 5) / (365 days)).safeCastTo32();
```

## 7. Incorrect `uint256()` casting

File: `AstariaRouter.sol` [Line 690 - Line 691](https://github.com/code-423n4/2023-01-astaria/blob/main/src/AstariaRouter.sol#L690-L691)

```solidity
52:  struct Details {
54:    uint256 rate; //rate per second
58:  }

690:    uint256 maxNewRate = uint256(stack[position].lien.details.rate) -
691:      s.minInterestBPS;
```

As seen above, `stack[position].lien.details.rate` is cast into a `uint256` when it is already of type `uint256 rate;`. However, `s.minInterestBPS`, which is type `uint32` ([defined here](https://github.com/code-423n4/2023-01-astaria/blob/main/src/interfaces/IAstariaRouter.sol#L74)) is not cast into a `uint256`.

With that in mind, line 690-691 could be refactored to:

```
690:    uint256 maxNewRate = stack[position].lien.details.rate -
691:    uint256(s.minInterestBPS);
```
