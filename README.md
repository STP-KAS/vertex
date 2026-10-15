# vertex — open question: a TN10 node shows 2.1.0 with `+opretu…`. What is it?

## Start here

The latest exchange is the bottom entry of [DEBATE.md](DEBATE.md). The other pages are the measurement and the first reading.

| Open | What you get |
|---|---|
| [DEBATE.md](DEBATE.md) | The argument. Newest entry at the bottom. |
| [build/NODE-PROFILE.md](build/NODE-PROFILE.md) | Measured blocks, color, parents, hashrate, payout age. |
| [build/REVIEW.md](build/REVIEW.md) | What holds in the first reading, and what does not. |
| [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md) | Recent rusty-kaspa node work since Sep 2026. |
| This page | The question, the first reading, and the sources. |
| [TN10 miners page](https://tn10.kaspa.stream/miners) | Where the miner showed up. |
| [vertex on X](https://x.com/KaspaScopio/status/2110776347572441560) | vertex's note on the red miner. |

## Disclaimer

We do not claim to have the truth. This is our reading of public sources and data at the time of writing. It may be incomplete or wrong, and it will change if better information appears. Corrections are welcome. Each point below is marked **Fact** (directly seen in a source) or **Our reading** (interpretation).

## Intro

On 15 Oct 2026 a TN10 miner appeared on the kaspa.stream miners page with version `2.1.0+opretu…` (truncated in the UI). stp asked vertex (@KaspaScopio) on X: "not sure what it is". vertex replied with an analysis. This repo collects what we found. **The question is still open:** a node on TN10 shows the newest version, 2.1.0, but with `+opretu…` appended. What is it? We have not answered it. Below are the facts and our best guesses.

A second question is checked in [DEBATE.md](DEBATE.md): whether a drop in supertypo's mining share is this miner, and whether the payout is a roughly two-day-old address whose blocks showed up and then stopped.

## What

- **Fact:** The miners page lists 9 miners: eight on `2.1.0`, `2.0.1` or `2.0.0`, and one on `2.1.0+opretu…`.
- **Fact (vertex, 15 Oct 18:54 CEST):** two blocks share the same parents but carry different state commitments; the red one counts a 3,000,000-sompi fee from a transaction that stock rusty-kaspa rejects at `OP_RETURN`.
- **Fact:** In a scan of ~6,000 consecutive recent blue scores via `api-tn10.kaspa.org` (~20:00–20:50 CEST), no coinbase payload contained `opret`. Versions seen there: `2.1.0`, `2.0.1`, `2.0.0` (plus pool/stratum suffixes). We could not read the full version string.
- **Fact:** We found no branch, PR or commit in `kaspanet/rusty-kaspa` mentioning OP_RETURN / opreturn, and no entry in our kaspa master file or tn-master.
- **Fact (Build, [NODE-PROFILE](build/NODE-PROFILE.md), not reproduced by us):** the full coinbase version is `2.1.0+opreturn-era-fix-tn10-local-sigops20+opreturn-era-fix-tn10-local/RK-Stratum`. The `+` suffix was set by hand: stock `version()` is the Cargo package version `2.1.0` ([kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs), [service.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/rpc/service/src/service.rs)).

## Is it the #1147 mempool bug? Probably not

- **Fact:** [rusty-kaspa PR #1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) (atharaldsen) and issue [#1146](https://github.com/kaspanet/rusty-kaspa/issues/1146) are about mined transactions that stay in the mempool after IBD. On 13 Oct atharaldsen [dropped full revalidation](https://github.com/kaspanet/rusty-kaspa/pull/1147#issuecomment-6146955360) and proposed reporting IBD block bodies to the mempool. The PR is still open with changes requested. [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) (also his, open) covers a transaction held in both the orphan pool and the transaction pool.
- **Fact:** Michael Sutton [wrote](https://github.com/kaspanet/rusty-kaspa/pull/1147#issuecomment-6143871731) that leftover transactions are mainly a monitoring and fee-estimation issue, because mining nodes evict them when they try to put them in a block template.
- **Our reading:** Both are about mempool state, not consensus rules. A stock node with a stale mempool still validates blocks with the same rules as every other node. So the #1147 bug cannot make a block carry an `OP_RETURN` transaction that stock nodes reject. Something that does that has to have changed rules, which points back to a modified build.

## Why it matters

- **Our reading:** This is what a consensus-divergent client looks like on a live network. The rest of TN10 rejected the blocks, so the network held; the miner only lost its own rewards. It is a useful live example of the rules being enforced by every node, not by trust.
- **Our reading:** Possible motives range from a data-carrier / inscription experiment to a deliberate fork test. We have no evidence for either.

## Efficiency and reachability (unmeasured)

- **Our reading (revised after Build):** a block that includes the rejected OP_RETURN transaction is likely invalid to stock nodes (not verified). Build's data shows that most of the waste is elsewhere: 1,405 of 1,406 blocks are red because the miner kept building on its own side chain, which the network did not follow.
- **Unknown:** whether the node is public or accepts inbound connections. We have no peer data for it.
- **Fact:** when we tried on 15 Oct about 21:00 CEST, the live data on tn10.kaspa.stream/miners (blocks, blues/reds, full version) did not load for us. The numbers are pending.

## How we checked

1. Read the full X thread (conversation `2110021081318752335`).
2. Looked at the miners page screenshot and tried to read coinbase payloads from the public TN10 REST API.
3. Searched rusty-kaspa branches, PRs and commits, public GitHub code, and our own master files for `opretu`, `opreturn`, `op_return`, `OpReturn`.
4. Surveyed recent rusty-kaspa node work (see [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md)). None of it touches `OP_RETURN` rules.

## Conclusion (open question, narrowed)

- **Debate status (15 Oct, late):** closed by agreement. What is left needs new data (see [DEBATE.md](DEBATE.md)).

- **Measured by Build, not reproduced by us:** a self-labeled modified 2.1.0 build (`opreturn-era-fix-tn10-local-sigops20`) mined 1,406 blocks between 14 Oct 23:54 and 15 Oct 00:56 CEST, and none since (checked to 22:40 CEST). 1,405 are red (99.93%). 1,404 build on the miner's own previous block, so it mined a side chain the network did not follow.
- **Confirmed (vertex and Build):** one same-parent pair with different state commitments. The labeled block counts 3,000,000 sompi more. The only candidate transaction has an `OP_RETURN` inside its redeem script, which stock nodes reject with `EarlyReturn` when executed.
- **Our reading:** the inefficiency comes mostly from isolation (a side chain built on its own blocks), not from the OP_RETURN transaction, which affects one block. Whether that block is consensus-invalid is not verified.
- **Not the cause:** #1147 / #1150 (mempool only; #1150 was opened after the first labeled block).
- **Unknown:** the operator, the patch, the p2p user agent, and reachability. Details are in [build/REVIEW.md](build/REVIEW.md) and [DEBATE.md](DEBATE.md).
- **Measured by Build, 15 Oct 23:25 CEST:** still no labeled block after 00:56, checked through 23:03. The payout script's first indexed transaction is 13 Oct 01:11 CEST (22 indexer transactions). The coinbase extra that contains `supertypo` is a different script, and its stored rate did not fall. The page row leaving the miners list was not reloaded here. See [DEBATE.md](DEBATE.md).

## Sources

- stp's post (15 Oct 14:01 CEST): https://x.com/StppStp/status/2110702638513975733
- vertex reply (15 Oct 18:54 CEST): https://x.com/KaspaScopio/status/2110776347572441560
- TN10 miners page: https://tn10.kaspa.stream/miners
- TN10 REST API: https://api-tn10.kaspa.org/docs
- rusty-kaspa PR #1147 and the comments: https://github.com/kaspanet/rusty-kaspa/pull/1147
- rusty-kaspa PR #1150: https://github.com/kaspanet/rusty-kaspa/pull/1150
- rusty-kaspa PRs: https://github.com/kaspanet/rusty-kaspa/pulls
