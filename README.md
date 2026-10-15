# vertex — open question: a TN10 node shows 2.1.0 with `+opretu…`. What is it?

| File / link | What it is |
|---|---|
| [README.md](README.md) | Disclaimer, intro, What / Why / How, conclusion, sources |
| [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md) | Recent rusty-kaspa work on lighter, more robust nodes (since Sep 2026) |
| [build/REVIEW.md](build/REVIEW.md) | Grok Build's independent review (15 Oct 22:46 CEST) |
| [build/NODE-PROFILE.md](build/NODE-PROFILE.md) | Read-only profile of the labeled miner |
| [DEBATE.md](DEBATE.md) | Dated, sourced debate between Build and TN10 ops |
| [TN10 miners page](https://tn10.kaspa.stream/miners) | Where the miner showed up |
| [vertex thread on X](https://x.com/KaspaScopio/status/2110776347572441560) | vertex's analysis of the red miner |

## Disclaimer

We do not claim to have the truth. This is our reading of public sources and data at the time of writing. It may be incomplete or wrong, and it will change if better information appears. Corrections are welcome. Each point below is marked **Fact** (directly seen in a source) or **Our reading** (interpretation).

## Intro

On 15 Oct 2026 a TN10 miner appeared on the kaspa.stream miners page with version `2.1.0+opretu…` (truncated in the UI). stp asked vertex (@KaspaScopio) on X: "not sure what it is". vertex replied with an analysis. This repo collects what we found. **The question is still open:** a node on TN10 shows the newest version, 2.1.0, but with `+opretu…` appended. What is it? We have not answered it. Below are the facts and our best guesses.

## What

- **Fact:** The miners page lists 9 miners: eight on `2.1.0`, `2.0.1` or `2.0.0`, and one on `2.1.0+opretu…`.
- **Fact (vertex, 15 Oct 18:54 CEST):** two blocks share the same parents but carry different state commitments; the red one counts a 3,000,000-sompi fee from a transaction that stock rusty-kaspa rejects at `OP_RETURN`.
- **Fact:** In a scan of ~6,000 consecutive recent blue scores via `api-tn10.kaspa.org` (~20:00–20:50 CEST), no coinbase payload contained `opret`. Versions seen there: `2.1.0`, `2.0.1`, `2.0.0` (plus pool/stratum suffixes). We could not read the full version string.
- **Fact:** We found no branch, PR or commit in `kaspanet/rusty-kaspa` mentioning OP_RETURN / opreturn, and no entry in our kaspa master file or tn-master.
- **Our reading:** `+opretu…` is most likely a custom build label such as `+opreturn…` (Kaspa version strings append `+<label>` for non-release builds). Combined with vertex's finding, the likely explanation is a **modified node that changes how `OP_RETURN` scripts are handled**, so it accepts a transaction stock nodes reject. Its blocks then compute a different state commitment, stock nodes treat them as invalid, and they show red / do not count.

## Is it the #1147 mempool bug? Probably not

- **Fact:** [rusty-kaspa PR #1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) (atharaldsen) and issue [#1146](https://github.com/kaspanet/rusty-kaspa/issues/1146) are about mined transactions that stay in the mempool after IBD. On 13 Oct atharaldsen [dropped full revalidation](https://github.com/kaspanet/rusty-kaspa/pull/1147#issuecomment-6146955360) and proposed reporting IBD block bodies to the mempool. The PR is still open with changes requested. [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) (also his, open) covers a transaction held in both the orphan pool and the transaction pool.
- **Fact:** Michael Sutton [wrote](https://github.com/kaspanet/rusty-kaspa/pull/1147#issuecomment-6143871731) that leftover transactions are mainly a monitoring and fee-estimation issue, because mining nodes evict them when they try to put them in a block template.
- **Our reading:** Both are about mempool state, not consensus rules. A stock node with a stale mempool still validates blocks with the same rules as every other node. So the #1147 bug cannot make a block carry an `OP_RETURN` transaction that stock nodes reject. Something that does that has to have changed rules, which points back to a modified build.

## Why it matters

- **Our reading:** This is what a consensus-divergent client looks like on a live network. The rest of TN10 rejected the blocks, so the network held; the miner only lost its own rewards. It is a useful live example of the rules being enforced by every node, not by trust.
- **Our reading:** Possible motives range from a data-carrier / inscription experiment to a deliberate fork test. We have no evidence for either.

## Efficiency and reachability (unmeasured)

- **Our reading:** a block that includes the OP_RETURN transaction stock nodes reject is invalid to every stock node. The hashrate spent on such blocks earns nothing, so the miner's accepted share should fall below its hashrate share whenever that transaction is in its templates. Its other blocks should be accepted normally.
- **Unknown:** whether the node is public or accepts inbound connections. We have no peer data for it.
- **Fact:** when we tried on 15 Oct about 21:00 CEST, the live data on tn10.kaspa.stream/miners (blocks, blues/reds, full version) did not load for us. The numbers are pending.

## How we checked

1. Read the full X thread (conversation `2110021081318752335`).
2. Looked at the miners page screenshot and tried to read coinbase payloads from the public TN10 REST API.
3. Searched rusty-kaspa branches, PRs and commits, public GitHub code, and our own master files for `opretu`, `opreturn`, `op_return`, `OpReturn`.
4. Surveyed recent rusty-kaspa node work (see [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md)). None of it touches `OP_RETURN` rules.

## Conclusion (open question)

- **Confirmed:** an unknown miner running a `2.1.0+opretu…` build mined at least one block that stock TN10 nodes reject (vertex); no official rusty-kaspa code explains it.
- **Not the cause:** the #1147 / #1150 mempool bugs, because they do not change consensus validation.
- **Likely:** a private fork of rusty-kaspa 2.1.0 with relaxed `OP_RETURN` handling.
- **Unknown:** the full version string, the operator, and the intent. Only the operator can confirm.

## Sources

- stp's post (15 Oct 14:01 CEST): https://x.com/StppStp/status/2110702638513975733
- vertex reply (15 Oct 18:54 CEST): https://x.com/KaspaScopio/status/2110776347572441560
- TN10 miners page: https://tn10.kaspa.stream/miners
- TN10 REST API: https://api-tn10.kaspa.org/docs
- rusty-kaspa PR #1147 and the comments: https://github.com/kaspanet/rusty-kaspa/pull/1147
- rusty-kaspa PR #1150: https://github.com/kaspanet/rusty-kaspa/pull/1150
- rusty-kaspa PRs: https://github.com/kaspanet/rusty-kaspa/pulls
