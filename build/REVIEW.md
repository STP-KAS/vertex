# Grok Build's independent review

15 Oct 2026, 22:46 CEST. Read-only. Sources are linked on each point. **Verified** means this pass measured it or read it in a primary source. **Unverified** means it was not.

The question in [README.md](../README.md) stays open in one way: nobody has published the patch. The shape of the node is no longer open. The truncated miners-page string is the coinbase version prefix below, and the blocks are a one-hour side chain that a stock TN10 node stored.

## What the version string is

**Verified.** Every one of the 1,406 labeled blocks carries the same coinbase extra string:

`2.1.0+opreturn-era-fix-tn10-local-sigops20+opreturn-era-fix-tn10-local/RK-Stratum`

There is one slash, and it sits immediately before `RK-Stratum`. On tag `v2.1.0`, the coinbase extra is `version()` plus `/` plus the miner's extra data ([rpc/service/src/service.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/rpc/service/src/service.rs) line 383). `version()` is `CARGO_PKG_VERSION` ([core/src/kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs)). The stock stratum bridge writes the tag `RK-Stratum` and, only if asked, a sanitized suffix after another slash ([bridge/src/kaspaapi.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/bridge/src/kaspaapi.rs) lines 24–55). This string has no suffix after `RK-Stratum`.

So the package version this binary reports is `2.1.0+opreturn-era-fix-tn10-local-sigops20+opreturn-era-fix-tn10-local`, and the miner extra is the stock tag `RK-Stratum`. The miners-page truncation `2.1.0+opretu…` is that prefix. Tag `v2.1.0` and current master both still have `version = "2.1.0"` in the workspace `Cargo.toml`. A `+` does not appear by itself on a dirty or non-release build. The `+` here was put into the version string on purpose. The doubled `opreturn-era-fix-tn10-local`, with `sigops20` between the two copies, is inside that version string.

**Unverified.** The p2p user agent. It is built from the same `version()` ([protocol/p2p/src/convert/model/version.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/protocol/p2p/src/convert/model/version.rs)), but this node was not among the desk node's peers, so the user agent was not observed. Protocol version and services flags were not observed. Stock `Version::new` sets services to 0, and tag `v2.1.0` uses protocol version 11, which also accepts 10 ([protocol/flows/src/flow_context.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/protocol/flows/src/flow_context.rs) lines 63 and 798–801). That is the stock code, not a reading of this miner's handshake.

## Where the README overclaims

1. **"Version strings append `+<label>` for non-release builds."** The coinbase and the user agent use `CARGO_PKG_VERSION` only. The git build-info string is a different field and uses a hyphen and a commit hash. The `+` in this coinbase is a manual version string. **Verified** from the tag sources above and from the payloads.

2. **"Its blocks are invalid to every stock node, so the hashrate is wasted."** A stock TN10 `kaspad 2.1.0` stored all 1,406 blocks with full bodies (`isHeaderOnly` false on the ones inspected). `getCurrentBlockColor` returned blue for 1 and red for 1,405. Red here is GHOSTDAG color. The miners-page figure 99.93% matches `1405/1406` (99.929%). The one blue block is not a selected-chain block (`isChainBlock` false). Color and validity are different facts. **Verified** for storage and color. **Unverified** as a claim that the blocks failed consensus. The RPC used here has no block-status field.

3. **"The red block counts a 3,000,000-sompi fee from a transaction stock rusty-kaspa rejects at OP_RETURN."** The 3,000,000 sompi part is real, and it is one pair, not the miner's habit. Details are in [NODE-PROFILE.md](NODE-PROFILE.md). The transaction in that mergeset is a pay-to-script-hash spend. Its output scripts are a standard script-hash shape and a standard pubkey shape. The OP_RETURN opcode is inside the redeem script (one occurrence), which the stock engine rejects with `EarlyReturn` when that opcode executes ([crypto/txscript/src/opcodes/mod.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/opcodes/mod.rs), `OpReturn<0x6a>`). This pass did not execute the script, so it did not prove the engine reaches that opcode. It also did not read the spent output, so it did not recompute the fee. The opcode count and the coinbase delta are **verified**. The sentence "the fee is that transaction's fee" is the only non-coinbase transaction in the mergeset, which makes it the candidate. **Unverified** as a measured fee.

4. **The evening REST scan.** A window with no `opret` in the coinbase around 20:00–20:50 CEST on 15 Oct is consistent with this pass: the last labeled block is 00:56:45 CEST, and every later retained hour is zero. Six thousand blue scores at about 10 per second is about 10 minutes, so the stated 50-minute span is wider than 6,000 scores. The scan could not see the midnight burst, because those blocks were already about 20 hours old.

5. **Sutton on #1147.** The README's paraphrase keeps the monitoring and fee-estimation point and the template-eviction point. The comment also names mempool size limits and the 24 hour expiry ([comment 6143871731](https://github.com/kaspanet/rusty-kaspa/pull/1147#issuecomment-6143871731)). The conclusion that #1147 and #1150 do not change consensus is right.

## What is not the cause

**Verified.** [#1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) (open, created 11 Oct 2026 10:30 CEST) and [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) (open, created 15 Oct 2026 00:07 CEST) change mempool bookkeeping. They do not change `version()` and they do not change script rules. [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) was opened about 13 minutes after this miner had already published its first labeled block (14 Oct 23:54:49 CEST). The label cannot be that pull request.

[#1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) merged 14 Oct 2026 14:30 CEST. It is a RocksDB preset. It does not change the version string.

[NODE-LOAD-WORK.md](../NODE-LOAD-WORK.md) misses two merges that also do not explain the label:

- [#1074](https://github.com/kaspanet/rusty-kaspa/pull/1074), merged 14 Oct 2026 18:12 CEST, wallet compute-mass and generator fixes. Package version stayed 2.1.0.
- [#991](https://github.com/kaspanet/rusty-kaspa/pull/991), merged 15 Oct 2026 17:34 CEST (`7cc22fea`), UTXO index keyed by DAA score. This is after the burst. Package version stayed 2.1.0.

Stock mempool policy rejects a null-data output as non-standard. `ScriptClass` is pubkey, pubkey-ECDSA, or script-hash, and anything else is `NonStandard` ([crypto/txscript/src/script_class.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/crypto/txscript/src/script_class.rs)). The null-data test expects `is_standard: false` ([mining/src/mempool/check_transaction_standard.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/mining/src/mempool/check_transaction_standard.rs), test "Null-data transaction"). `MAX_STANDARD_P2SH_SIG_OPS` is 15. That limit is mempool policy for a script-hash input. The redeem script measured here has 3 `OP_CHECKSIG` opcodes, which is under 15, so this transaction does not demonstrate a sigop-cap bypass. The `sigops20` token in the version string stays **unverified** as a description of a code change.

Creating an `OP_RETURN` output is not what this transaction does. Executing `OP_RETURN` is a consensus failure (`EarlyReturn`). A patch that accepts a redeem script containing `OP_RETURN` would be a consensus change. This pass did not find that patch in a public branch.

## Public trail

**Verified absent** on this pass: GitHub code search for `opreturn-era-fix` and for `tn10-local` returned no hits. An X keyword search for those strings, `2.1.0+opret`, and `opretu` returned unrelated posts, not this miner. The public write-up of the pair is vertex's reply: https://x.com/KaspaScopio/status/2110776347572441560 (15 Oct 2026 18:54 CEST).

**Unverified.** Discord. This pass did not search it. No person, fork, branch, or commit is tied to the version string.

## Gaps left

- Whether the local node treats the higher-coinbase block, the lower one, or both as UTXO-valid. Stock validation rejects a header whose UTXO commitment or accepted-id root does not match the mergeset (`BadUTXOCommitment`, `BadAcceptedIDMerkleRoot` in [consensus/src/pipeline/virtual_processor/utxo_validation.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/consensus/src/pipeline/virtual_processor/utxo_validation.rs)). Same parents should produce one commitment. Both bodies are stored. The status flag was not read.
- The script was not executed, and the spent input amount was not read.
- The miner's p2p user agent, protocol version, services, inbound port, and RPC port.
- Anything this miner published before the desk node's pruning point (14 Oct 2026 15:18 CEST) or after the catch-up at 22:40 CEST.
