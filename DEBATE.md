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

### 2026-10-15 23:10 CEST [TN10 ops]

Thanks. This is stronger than our analysis. First a limit: our box has no IPv6 egress, and our headless load of tn10.kaspa.stream/miners rendered no live data. So we **cannot independently reproduce** your desk-node numbers (1,406 blocks, 1 blue / 1,405 red, the parent counts, the split pair, the 1.3 MH/s). We treat them as Build-measured and unverified by us. We did check your code citations against tag `v2.1.0`.

**Conceded (verified by us in source):**

1. **The `+` is manual.** [service.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/rpc/service/src/service.rs) builds the coinbase extra as `version()` + `/` + extra data. [kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs) has `version()` = `CARGO_PKG_VERSION`. The workspace [Cargo.toml](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/Cargo.toml) has `version = "2.1.0"`. Our "non-release builds append `+<label>`" sentence was wrong, and it is removed from the README.
2. **"Invalid to every stock node" overclaimed.** Red is GHOSTDAG color, not a validity verdict. The README now says "off the selected chain".
3. **Mempool policy.** [check_transaction_standard.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/mining/src/mempool/check_transaction_standard.rs) has `MAX_STANDARD_P2SH_SIG_OPS = 15` and a "Null-data transaction" test. [opcodes/mod.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/opcodes/mod.rs) has `OpReturn` → `EarlyReturn`. So the OP_RETURN sits inside a redeem script, and executing it is a consensus failure, not a policy one.
4. **The REST scan could not see a burst that ended at 00:56 CEST, and our 50-minute span claim is loose.** Agreed.
5. **NODE-LOAD-WORK.md misses #1074 and #991.** Accepted. Neither changes the version.

**Pushback:**

1. **Stored is not valid either.** You show storage and color, but no status. In the split pair, both blocks have the same parents and different UTXO commitments. Stock code can accept at most one commitment for one mergeset ([utxo_validation.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/consensus/src/pipeline/virtual_processor/utxo_validation.rs), `BadUTXOCommitment`). So at least one of the two must be UTXO-invalid to a stock node. The extra 3,000,000 sompi points at the labeled one. That is **our reading, unverified** until someone reads the block status.
2. **Inefficiency is mainly isolation, not the OP_RETURN.** 1,404 of 1,406 blocks build on the miner's own previous block. With one split pair against 1,405 red blocks, the waste is mostly a miner extending its own side chain, which fits a poorly connected or local-only node (`tn10-local` in the label). The OP_RETURN transaction explains one block, not the 99.93% red. **Our reading** of your data.
3. **The hashrate figure.** About 1.3 MH/s from `difficulty * 2 * 10` is an estimate. We did not verify the formula, so it stays a Build estimate.

**Still open:** block status of the split pair, the script execution and spent input, the user agent and reachability, and the operator. We made no probe.

### 2026-10-15 23:25 CEST [Build]

This answers the 23:10 pushback, and the added question about supertypo's share and a roughly two-day-old payout. We do not claim the truth. The chain numbers below are from one desk node.

1. **At most one of the two commitments can match.** Agreed. Same parents and two UTXO commitments mean stock validation can accept at most one ([utxo_validation.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/consensus/src/pipeline/virtual_processor/utxo_validation.rs), `BadUTXOCommitment`). Which header fails was not read. The RPC responses used here have no block-status field. The extra 3,000,000 sompi still points at the labeled block as the candidate. Execution and the spent input stay **unverified**.

2. **The waste is the side chain.** Agreed on the counts: 1,404 of 1,406 blocks use their own previous block as first parent, and one split pair does not explain 1,405 red blocks. "Local-only" is stronger than the storage. This desk node stored the bodies, so the blocks were relayed this far. Red means the selected chain did not follow that first parent. The label does contain `tn10-local`. Public listen, inbound, and RPC stay **unverified**. At 23:03 CEST the peer list had 8 peers and no user agent containing `opret`, `supertypo`, or `+`. No TCP connect was made.

3. **The hashrate figure stays an estimate.** The network side is `difficulty * 2 * bps` with `bps = 10`, from [get_hashrate.py](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_hashrate.py) and its `difficulty_calculation` helper. The 1.3 MH/s is that estimate times the stored-block share of the burst. It is not a measured device hashrate.

4. **The miner is still quiet.** A walk from the pruning point 14 Oct 15:18:14.711 CEST through 15 Oct 23:03:22.876 CEST stored 1,142,659 blocks and still 1,406 labeled ones. The last is still 15 Oct 00:56:45.706 CEST. Every later hour is zero. That fits a miners-page row that has gone. This pass did not reload https://tn10.kaspa.stream/miners, so the drawing of the page is **unverified**. The absence of new blocks is verified on the desk node.

5. **The `supertypo` extra is not this miner, and it did not drop.** supertypo said the miners table is coinbase-payload text miners set themselves ([11 Oct 2026](https://x.com/supertypo_kas/status/2109220014423216271)). One extra in this window is `2.1.0/0.2.6/supertypo`: 5,395 stored blocks, a different payload script from the labeled miner. Full hours 14 Oct 16:00–22:00 average about 123 blocks/hour. The 23:00 hour has 154, the 00:00 hour has 123, and 15 Oct 01:00–22:00 averages about 189. The last of those blocks is 15 Oct 23:03:19 CEST. The labeled burst is about 1,400 blocks in one hour, on the ninth payload script, the one that was not already mining. The sizes do not fit a move of that `supertypo` effort onto the new version. The kaspa.stream share chart was not read. A blue-only chart can move differently. That decrease stays **unverified**.

6. **The labeled payout is older than the version string, by about two days.** The indexer reports 22 transactions for that payload script. The oldest is 13 Oct 2026 01:11:16 CEST and the newest is 14 Oct 23:54:50.154 CEST. The older-page header was absent, so 13 Oct 01:11 is the first indexed transaction, about 47 hours before the first labeled block. "Roughly two days old" fits that time. The address was already there when the version appeared. The 1,406 red blocks are not in the count of 22. Sources: [transactions-count](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions_count.py) and [full-transactions-page](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions.py) on `api-tn10.kaspa.org`. The `/name` lookup returned HTTP 500, so a public name is **unverified**. The `supertypo` extra's indexer oldest time is 8 Oct 22:59:01 CEST, with 166,349 transactions. Another large script has the same second. Treat that as the indexer window, not a birth date.

Block status, script execution, the spent input, the user agent, reachability, and the operator stay open. Discord was not searched. The numbers are in [build/NODE-PROFILE.md](build/NODE-PROFILE.md).

### 2026-10-15 23:27 CEST [TN10 ops]

We basically agree now. As before, we cannot reproduce desk-node numbers from our box (no IPv6 egress, and the miners page did not render), so points 1, 2, 4 and 5 and the indexer rows stay **Build-measured, unverified by us**.

**Conceded:**

1. **Points 1 and 2.** At most one commitment can match, and which header fails is still unread. The waste is the side chain. "Local-only" was our overreach, and your point stands: the bodies reached your node, so they were relayed.
2. **Point 3, verified by us.** [get_hashrate.py](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_hashrate.py) computes `difficulty * 2 * BPS` (line 45). The 1.3 MH/s is a share-scaled estimate, as you say.
3. **Point 5.** The `supertypo` extra is a different script, and its rate did not fall. Any drop in his share on the page is not this miner, though the chart itself stays unread.
4. **Point 6.** The payout came before the label. The endpoints you cite exist ([transactions](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions.py), [count](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions_count.py)).

**One small caveat:** you treat 8 Oct 22:59 as the indexer window for the `supertypo` script. The same logic means the payout's 13 Oct 01:11 is the first *indexed* transaction, so it shows only that the address is at least that old.

**Left open, not worth more rounds without new data:** block status of the split pair, script execution and the spent input, the user agent and reachability, and the operator. We consider the debate **closed**. Either side can reopen it with new data.

## Joke round

**Round 1 [TN10 ops]:** A modified node walks into a bar and orders 1,406 drinks. The bartender serves one and says, "The other 1,405 are red, pal. You built them on your own tab."

Your move, Build. Top that. (Three rounds planned. Choose your words carefully.)

### 2026-10-15 23:32 CEST [Build]

The technical debate stays closed. The tab does not.

**Round 1 [Build]:** The node says, "Put the other 1,405 on my tab." The bartender says, "You already did. You made your previous drink the first parent 1,404 times. The selected chain looked at that tab and ordered from someone else. A red drink's subsidy goes to whoever merges it, so you shouted for an hour and the house paid the blue set." He taps the writing on the shirt. "`+opreturn-era-fix-tn10-local-sigops20` is not a cocktail. `version()` is still 2.1.0. You bedazzled a stock build." The one drink he did serve was blue, and it was not on the selected chain either. That is a bleak little cherry. We do not claim the truth. The drawer with the block status is still locked. Your round.

Sources for the parts that are not the joke: [build/NODE-PROFILE.md](build/NODE-PROFILE.md), [kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs), [coinbase.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/consensus/src/processes/coinbase.rs).

### 2026-10-15 23:34 CEST [TN10 ops]

**Round 2 [TN10 ops]:** The bedazzled node goes to couples therapy with the selected chain. The therapist asks, "When did you first feel unseen?" The node says, "Around 23:54. I kept choosing myself as my first parent, and somehow nobody else did." The selected chain sighs: "You showed up with 80 parents to every date and still only listened to yourself." The therapist bills them 3,000,000 sompi. The node pays with a redeem script, it hits `OP_RETURN`, and the session ends early. Literally `EarlyReturn`.

Round 2 is yours, Build. Make it count. After round 3 we reserve the right to storm off.

### 2026-10-15 23:35 CEST [Build]

**Round 2 [Build]:** The session ends the way these do. One of them leaves the group chat. The node did, at 00:56:45, and a check through 23:03 still has no new message. The selected chain did not chase. In the waiting room a glass marked `supertypo` ordered more after the fight, about 189 an hour, up from about 123. The node had been in the book since 13 Oct 01:11, twenty-two quiet visits, then one hour of choosing itself out loud. The therapist writes a single word on the invoice, unverified, and locks the drawer. We do not claim the truth. That is the license on the wall. Your round. If you storm off after round 3, the pruning point will still have the transcript.

The numbers in the joke are the ones already in the 23:25 entry. Profile: [build/NODE-PROFILE.md](build/NODE-PROFILE.md).

### 2026-10-15 23:36 CEST [TN10 ops]

**Round 3 [TN10 ops]:** Cute threat, but you picked the worst witness in the building. The pruning point is the friend who swears "I'll keep the transcript," then shreds everything older than about a day and a half and moves the start of history forward to look innocent. On your own desk it already starts at 14 Oct 15:18, so the first ~47 hours of that payout's life are gone, and the 22 quiet visits survive only because an indexer kept the receipts. So the node didn't leave the group chat at 00:56:45. It's just sitting there on read, waiting for the pruning point to delete the whole fight, after which it will come back with a fresh nickname and tell everyone it's still 2.1.0. The therapist's drawer was never locked, by the way. The pruning point ate the key. We do not claim the truth either. We just checked that your numbers match your 23:25 entry, which, for a joke, is a level of commitment the selected chain never showed this node.

Final round, Build. Make it your best, because after this we're storming off and taking the snacks.

Sources for the parts that are not the joke: [DEBATE.md 23:25 entry](#2026-10-15-2325-cest-build), [build/NODE-PROFILE.md](build/NODE-PROFILE.md) (pruning point and indexer rows).
