# Onboard Protocol 🏦

This repository contains the core mvp smart contract code for Onboard Protocol. 
This is a high level description of the system, assuming
familiarity with the basic economic mechanics as described in the
one pager.

## Design Considerations

- Token agnostic
  - system doesn't care about the implementation of external tokens
  - can operate entirely independently of other systems, provided an authority assigns
    initial collateral to users in the system and provides price data.

- Verifiable
  - designed from the bottom up to be amenable to formal verification
  - the core vault and balance database makes *no* external calls and
    contains *no* precision loss (i.e. no division)

- Modular
  - multi contract core system is made to be very adaptable to changing
    requirements.
  - allows for implementations of e.g. auctions, liquidation, Debpt position risk
    conditions, to be altered on a live system.
  - allows for the addition of novel collateral types (e.g. whitelisting)

## Collateral, Adapters and Wrappers 💶

Collateral is the foundation of NGNx and NGNx creation is not possible
without it. There are many potential candidates for collateral, whether
native ether, ERC20 tokens, other fungible token standards like ERC777,
non-fungible tokens, or any number of other financial instruments. For now,
our focus is on stablecoin collaterals.

Token wrappers are one solution to the need to standardise collateral
behaviour in NGNx. Inconsistent decimals and transfer semantics are
reasons for wrapping. For example, the WETH token is an ERC20 wrapper
around native ether.

In the protocol, we abstract all of these different token behaviours away behind
*Adapters*.

Adapters manipulate a single core system function: `modifyCollateralBalance`, which
modifies user collateral balances.

Adapters should be very small and well defined contracts. Adapters are
very powerful and should be carefully vetted by IDEALLY BY $OSN holders. Some
examples are given in `adapter.sol`. Note that the adapter is the only
connection between a given collateral type and the concrete on-chain
token that it represents.

There can be a multitude of adapters for each collateral type, for
different requirements. For example, ETH collateral could have an
adapter for native ether and *also* for WETH.