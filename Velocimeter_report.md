
## Velocimeter Audit Report

Velocimeter V4 is a `ve(3, 3)` dex with veLP, permissionless gauges, and an emission schedule that grows with demand. These new features are the focus of the contest.


| Number | Details |
|----------|----------|
| [H-1](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/348) | `create_lock(...)` can be DOS'd preventing users from creating lock in the `VotingEscrow`  |
| [H-2](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/367) | rewards are lost when merging and withdrawing tokens  |
| [H-3](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/398) | Claimable emissions are locked when a gauge is killed  |
| [H-4](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/498) | `checkpoint_total_supply()` can prematurely update the `veSupply[t]` and `time_cursor` leading to wrong reward calculations  |
| [M-1](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/168) | `update_period(..)` leads to wrong calculation in weekly emissions breaking accounting for the protocol  |
| [M-2](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/509) | Votes of expired token in gauges lead to dilution of FLOW emissions for gauges with active votes  |
| [M-3](https://github.com/sherlock-audit/2024-06-velocimeter-judging/issues/667) | The First liquidity provider of a stable pair can DOS the pool  |

