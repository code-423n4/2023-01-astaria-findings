# Misleading argument names in Validator contracts

## Summary

Certain arguments are named in a misleading manner in the Validator contracts, which may lead to current/future critical bugs due to human error.

## Details

The Validator contracts (in `core/strategies`) are supposed to verify claimed lien terms against the strategists' signed loan terms.

One of the checks it does, is for verifying if the collateral NFT's address, ID, etc match between the two.

However, with parameters named as `collateralTokenXXXXX`, it may mislead current/future developers into mistaking it as `CollateralToken`, which is another legitimate contract used by Astaria.

**Such mistakes may open up incorrect signature verification vulnerabilities in the future.**

## Mitigation

A simple change in naming of the same parameters is enough to solve the issue.

`collateralTokenXXXXX` maybe simply named as something like `nftAsCollateralXXXXX`.