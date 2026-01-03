## Autonomint Colored Dollar V1 Report


| Number | Details |
|----------|----------|
| [H-1] | [`usdaCollectedFromCdsWithdraw` is not updated during withdrawal for users who did not opt in for liquidation](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/527)  |
| [H-2] | [Users can steal USDT using `redeemUSDT()` from the CDS](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/646)  |
| [H-3] | [Liquidation can be blocked if `liqAmountToGetFromOtherChain == 0`](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/663)  |
| [H-4] | [All the interest earned from liquidation is stuck in the treasury forever](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/693)  |
| [H-5] | [missing `omniChainData.noOfLiquidations` update in `liquidationType2()` can lead to loss of CDS depositor who opt in for liquidation](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/718) |
| [H-6] | [option renewal can be done in less than 15 or after 30 days](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/721)  |
| [H-7] | [`lastEventTime` is not updated during liquidation and withdrawal thus overinflating the `lastCumulativeRate`](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/836)  |
| [M-1] | [wrong amount of `sUSD` is used to open a short position in synthetix](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/1007)  |
| [M-2] | [`submitOffchainDelayedOrder()` is called wrong with wrong paramters](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/1043)  |
| [M-3] | [CDS withdrawals can be blocked for depositors who opted for liquidation](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/515)  |
| [M-4] | [10% of CDS depositors profit are stuck in the treasury during withdrawal without a way to retrieve them](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/526)  |
| [M-5] | [The interest from external protocol during liquidation is stuck without a way to withdraw](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/687) |
| [M-6] | [Yield form LRTs are forever stuck in the protocol and cannot be withdrawn](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/818)  |
| [M-7] | [`downsideProtected` can block withdrawals anytime funds are requested from other chains to cover downside](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/873)  |
| [M-8] | [excess funds will not always be refunded to borrower when they are withdrawing](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/890)  |
| [M-9] | [`liquidationType2()` will always due wrong assumption that Asset:sAsset are minted 1:1](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/962)  |
| [M-10] | [The protocol does not consider Sythetix exchange fees during liquidation](https://github.com/sherlock-audit/2024-11-autonomint-judging/issues/983)  |
