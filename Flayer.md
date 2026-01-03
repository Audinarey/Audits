
## [Flayer](https://audits.sherlock.xyz/contests/468?filter=questions) Audit Report
ƒlayer is a liquidity protocol for NFTs with custom Uniswap V4 hook, donate and pool integrations. This audit is to ensure the absolute security in our codebase for the ƒlayer protocol and the Moongate bridge.


| Number | Details |
|----------|----------|
| [H-1](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/519) | `CollectionShutdown::execute(...)` can be permanently bricked thus blocking liquidation for a collection  |
| [H-2](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/532) | Funds will be stuck in the `CollectionShutdown` contract if shutdown execution is cancelled  |
| [H-3](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/534) | `params.quorumVotes` can overflow  |
| [M-1](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/487) | Anyone can DOS `CollectionShutdown::preventShutdown()`  |
| [M-2](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/671) | Users can manipulate interest rate using their protected listings  |
| [M-3](https://github.com/sherlock-audit/2024-08-flayer-judging/issues/719) | user can create listing with dust amount to block collection shutdown  |

