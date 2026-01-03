## [Autonomint](https://audits.sherlock.xyz/contests/569?filter=questions) Colored Dollar V1 Report

A delta-neutral stablecoin, fully backed by crypto assets and offered at 100% synthetic LTV, with risks hedged through decentralized credit default swaps (dCDS).


| Number | Details |
|----------|----------|
| [H-1](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/527) | `usdaCollectedFromCdsWithdraw` is not updated during withdrawal for users who did not opt in for liquidation  |
| [H-2](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/646) | Users can steal USDT using `redeemUSDT()` from the CDS  |
| [H-3](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/663) | Liquidation can be blocked if `liqAmountToGetFromOtherChain == 0`  |
| [H-4](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/693) | All the interest earned from liquidation is stuck in the treasury forever  |
| [H-5](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/718) | missing `omniChainData.noOfLiquidations` update in `liquidationType2()` can lead to loss of CDS depositor who opt in for liquidation |
| [H-6](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/721) | option renewal can be done in less than 15 or after 30 days  |
| [H-7](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/836) | `lastEventTime` is not updated during liquidation and withdrawal thus overinflating the `lastCumulativeRate`  |
| [M-1](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/1007) | wrong amount of `sUSD` is used to open a short position in synthetix  |
| [M-2](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/1043) | `submitOffchainDelayedOrder()` is called wrong with wrong paramters  |
| [M-3](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/515) | CDS withdrawals can be blocked for depositors who opted for liquidation  |
| [M-4](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/526) | 10% of CDS depositors profit are stuck in the treasury during withdrawal without a way to retrieve them  |
| [M-5](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/687) | The interest from external protocol during liquidation is stuck without a way to withdraw |
| [M-6](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/818) | Yield form LRTs are forever stuck in the protocol and cannot be withdrawn  |
| [M-7](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/873) | `downsideProtected` can block withdrawals anytime funds are requested from other chains to cover downside  |
| [M-8](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/890) | excess funds will not always be refunded to borrower when they are withdrawing  |
| [M-9](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/962) | `liquidationType2()` will always due wrong assumption that Asset:sAsset are minted 1:1  |
| [M-10](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/983) | The protocol does not consider Sythetix exchange fees during liquidation  |
