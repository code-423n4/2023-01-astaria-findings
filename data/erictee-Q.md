### [L-01] Pragma experimental abiencoderv2 is deprecated.


#### Impact
Use ```pragma abicoder v2``` [instead.](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics)


#### Findings:
```
src/LienToken.sol:L16         pragma experimental ABIEncoderV2;

src/CollateralToken.sol:L16         pragma experimental ABIEncoderV2;

```

### [L-02] ```decimals()``` not part of ERC20 standard.


#### Impact
```decimals()``` is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.


#### Findings:
```
src/WithdrawProxy.sol:L273        10**ERC20(asset()).decimals()

src/PublicVault.sol:L103    if (ERC20(asset()).decimals() == uint8(18)) {

src/PublicVault.sol:L106      return 10**(ERC20(asset()).decimals() - 1);

```

### [L-03] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
src/VaultImplementation.sol:L246    address recovered = ecrecover(

```

### [L-04] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
src/WithdrawProxy.sol:L139    _mint(receiver, shares);

src/LienToken.sol:L455    _mint(params.receiver, newLienId);

src/PublicVault.sol:L512    _mint(msg.sender, unclaimed);

src/CollateralToken.sol:L588      _mint(from_, collateralId);

```
