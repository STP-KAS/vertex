# Recent rusty-kaspa work on lighter, more robust nodes (since Sep 2026)

Times in CEST. Status as of 15 Oct 2026. None of these change `OP_RETURN` rules.

| PR | Author | Status | What it does |
|---|---|---|---|
| [#1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) | supertypo | merged 14 Oct 14:30 | `lowio` RocksDB preset: ~50% fewer bytes written, ~99% fewer compactions, ~10% faster IBD, a bit more RAM |
| [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) | atharaldsen | open (15 Oct 00:07) | Removes orphan copy on mempool insert; fixes a mempool-full assert panic seen on TN10 under load |
| [#1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) | atharaldsen | open | Stale mempool txs after IBD; core flagged full-pool revalidation as a DoS risk, approach being reworked |
| [#1141](https://github.com/kaspanet/rusty-kaspa/pull/1141) | atharaldsen | open | Remove `TRANSIENT_BYTE_TO_MASS_FACTOR` |
| [#1142](https://github.com/kaspanet/rusty-kaspa/pull/1142) | atharaldsen | merged 13 Oct 12:19 | Replace `accepted_id_digests` Vec with SeqCommit |
| [#1138](https://github.com/kaspanet/rusty-kaspa/pull/1138) | someone235 | merged 22 Sep 15:44 | Arithmetic safety and more fixes (v2.1.0 bumped in #1139, 15:50) |
| [#1137](https://github.com/kaspanet/rusty-kaspa/pull/1137) | someone235 | merged 20 Sep 14:40 | Explicitly reject coinbase transactions from mempool |
| [#1136](https://github.com/kaspanet/rusty-kaspa/pull/1136) | someone235 | merged 20 Sep 13:14 | Split headers into 20 MiB chunks during IBD |
| [#1135](https://github.com/kaspanet/rusty-kaspa/pull/1135) | someone235 | merged 13 Oct 13:12 | Explicit opt-in for non-SIG_HASH_ALL PSKB signing (security) |
