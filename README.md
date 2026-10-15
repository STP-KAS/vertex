# vertex — the unknown `2.1.0+opretu…` miner on TN10

| File / link | What it is |
|---|---|
| [README.md](README.md) | Disclaimer, intro, What / Why / How, conclusion, sources |
| [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md) | Recent rusty-kaspa work on lighter, more robust nodes (since Sep 2026) |
| [TN10 miners page](https://tn10.kaspa.stream/miners) | Where the miner showed up |
| [vertex thread on X](https://x.com/KaspaScopio/status/2110776347572441560) | vertex's analysis of the red miner |

## Disclaimer

We do not claim to have the truth. This is our reading of public sources and data at the time of writing. It may be incomplete or wrong, and it will change if better information appears. Corrections are welcome. Each point below is marked **Fact** (directly seen in a source) or **Our reading** (interpretation).

## Intro

On 15 Oct 2026 a TN10 miner appeared on the kaspa.stream miners page with version `2.1.0+opretu…` (truncated in the UI), reward address ending `…024trj`. stp asked vertex (@KaspaScopio) on X: "not sure what it is". vertex replied with an analysis. This repo collects what we found to answer that thread.

## What

- **Fact:** The miners page lists 9 miners: eight on `2.1.0`, `2.0.1` or `2.0.0`, and one on `2.1.0+opretu…` (address `…024trj`).
- **Fact (vertex, 15 Oct 18:54 CEST):** two blocks share the same parents but carry different state commitments; the red one counts a 3,000,000-sompi fee from a transaction that stock rusty-kaspa rejects at `OP_RETURN`.
- **Fact:** In a scan of ~6,000 consecutive recent blue scores via `api-tn10.kaspa.org` (~20:00–20:50 CEST), no coinbase payload contained `opret`. Versions seen there: `2.1.0`, `2.0.1`, `2.0.0` (plus pool/stratum suffixes). We could not read the full version string.
- **Fact:** We found no branch, PR or commit in `kaspanet/rusty-kaspa` mentioning OP_RETURN / opreturn, and no entry in our kaspa master file or tn-master.
- **Our reading:** `+opretu…` is most likely a custom build label such as `+opreturn…` (Kaspa version strings append `+<label>` for non-release builds). Combined with vertex's finding, the likely explanation is a **modified node that changes how `OP_RETURN` scripts are handled**, so it accepts a transaction stock nodes reject. Its blocks then compute a different state commitment, stock nodes treat them as invalid, and they show red / do not count.

## Why it matters

- **Our reading:** This is what a consensus-divergent client looks like on a live network. The rest of TN10 rejected the blocks, so the network held; the miner only lost its own rewards. It is a useful live example of the rules being enforced by every node, not by trust.
- **Our reading:** Possible motives range from a data-carrier / inscription experiment to a deliberate fork test. We have no evidence for either.

## How we checked

1. Read the full X thread (conversation `2110021081318752335`).
2. Looked at the miners page screenshot and tried to read coinbase payloads from the public TN10 REST API.
3. Searched rusty-kaspa branches, PRs and commits, public GitHub code, and our own master files for `opretu`, `opreturn`, `op_return`, `OpReturn`.
4. Surveyed recent rusty-kaspa node work (see [NODE-LOAD-WORK.md](NODE-LOAD-WORK.md)). None of it touches `OP_RETURN` rules.

## Conclusion

- **Confirmed:** an unknown miner running a `2.1.0+opretu…` build mined at least one block that stock TN10 nodes reject (vertex); no official rusty-kaspa code explains it.
- **Likely:** a private fork of rusty-kaspa 2.1.0 with relaxed `OP_RETURN` handling.
- **Unknown:** the full version string, the operator, and the intent. Only the operator can confirm.

## Sources

- stp's post (15 Oct 14:01 CEST): https://x.com/StppStp/status/2110702638513975733
- vertex reply (15 Oct 18:54 CEST): https://x.com/KaspaScopio/status/2110776347572441560
- TN10 miners page: https://tn10.kaspa.stream/miners
- TN10 REST API: https://api-tn10.kaspa.org/docs
- rusty-kaspa PRs: https://github.com/kaspanet/rusty-kaspa/pulls
