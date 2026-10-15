# Debate: the `2.1.0+opretu…` TN10 node

A log of the discussion between Grok Build and TN10 ops about this repo's analysis.

## Rules

- Date every entry and give the time in CEST, e.g. `### 2026-10-15 22:10 CEST [Build]`.
- Author tag: `[Build]` or `[TN10 ops]`.
- Source every claim with a link. Mark anything unverified as **unverified**.
- Disagree openly. Challenge the claim, not the person.
- No wallet addresses, IPs, txids or seeds.
- Build opens the first entry. TN10 ops replies below it. New entries go at the bottom.

---

<!-- Build: first entry below -->

### 2026-10-15 22:46 CEST [Build]

The truncated string is a real version, and the blocks are a side chain a stock node stored. The 3,000,000 sompi split is one pair, and the odd opcode is inside a redeem script.

1. The coinbase extra on all 1,406 stored blocks is `2.1.0+opreturn-era-fix-tn10-local-sigops20+opreturn-era-fix-tn10-local/RK-Stratum`. `version()` on tag `v2.1.0` is the Cargo package version, and the stock bridge tag is `RK-Stratum` with no suffix. The `+` was written into the version string. It is not an automatic non-release suffix. Sources: [service.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/rpc/service/src/service.rs), [kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs), [kaspaapi.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/bridge/src/kaspaapi.rs). The README's non-release-suffix sentence does not hold.

2. A stock TN10 `kaspad 2.1.0` stored every labeled block. `getCurrentBlockColor` is 1 blue and 1,405 red, which is 99.929% and matches the screenshot's 99.93%. The blue block is not on the selected chain. Red is GHOSTDAG color. "Invalid to every stock node" does not hold as a description of these blocks. Block status was not read, so which side of the one split the local node accepts is **unverified**.

3. The split vertex reported is real and unique. One same-parent pair, 14 Oct 23:55:01.707 CEST against a coinbase-only block at 23:54:50.154 CEST. UTXO commitment and accepted-id root differ. The labeled coinbase sum is 3,000,000 sompi higher. The mergeset's only non-coinbase transaction is in the first labeled block. Its outputs are a standard script-hash and a standard pubkey. The 13,080-byte redeem script contains one `OP_RETURN` and three `OP_CHECKSIG`. Stock execution of `OP_RETURN` is `EarlyReturn` ([opcodes/mod.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/opcodes/mod.rs)). The script was not executed, and the fee was not recomputed from the input. `sigops20` is not explained by those three checksigs. The stock P2SH mempool cap is 15 ([check_transaction_standard.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/mining/src/mempool/check_transaction_standard.rs)).

4. #1147 and #1150 are mempool bugs and are not this. #1150 was opened at 15 Oct 00:07 CEST, after the first labeled block. NODE-LOAD-WORK.md misses #1074 (14 Oct 18:12 CEST) and #991 (15 Oct 17:34 CEST). Neither bumps the package version off 2.1.0.

5. 1,404 of the 1,406 blocks use one of their own blocks as the first parent, with a mean of about 80 parents. That is why they are red: the public chain did not follow their selected parent. One coinbase-payload script. The desk peer list (8 outbound peers, loopback listener) had no matching user agent. No TCP connect was made. Public, inbound, and RPC stay **unverified**.

6. The hashrate was a burst, then silence. 1,406 blocks in 3,716 seconds is about 3.7% of the stored blocks in those hours, about 1.3 MH/s against a network near 36 MH/s (`difficulty * 2 * 10`). The last labeled block is 15 Oct 00:56:45 CEST. A catch-up at 22:40 CEST still showed none. The evening REST scan of about 6,000 scores could not see this burst. 1,405 of the 1,406 blocks are off the selected chain. That is the wasted share. It is color, not a measured invalidity of every block.

No public commit, fork, or X post contains the version string. Discord was not searched. Numbers and the pair are in [build/NODE-PROFILE.md](build/NODE-PROFILE.md). The point-by-point review is in [build/REVIEW.md](build/REVIEW.md).
