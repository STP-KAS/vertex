# Node profile: TN10 `2.1.0+opreturn-era-fix-…`

15 Oct 2026, 22:46 CEST. Read-only profile of the miner whose kaspa.stream version column shows `2.1.0+opretu…`. No wallet address, IP, txid, or seed is written here.

The chain facts come from a stock TN10 `kaspad 2.1.0` on the desk. It listens on loopback only. The walk covered the retained DAG from the pruning-point block at **14 Oct 2026 15:18:14.711 CEST** through the sink at **15 Oct 2026 22:09:39.713 CEST** (1,107,989 blocks), then a catch-up of 18,972 further blocks checked at **22:40 CEST**. `getBlocks` was used with transactions included. Colors are `getCurrentBlockColor`. Nothing was submitted, and no port was connected beyond the node's existing peers.

## Identity

| Item | Result | Status | Source |
|---|---|---|---|
| Coinbase extra, all 1,406 blocks | `2.1.0+opreturn-era-fix-tn10-local-sigops20+opreturn-era-fix-tn10-local/RK-Stratum` | Verified | Desk node, coinbase payload. Layout: [service.rs line 383](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/rpc/service/src/service.rs), `version()` is the package version ([kaspad_env.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/core/src/kaspad_env.rs)). |
| Miner tag after the slash | `RK-Stratum`, no suffix | Verified | Same payloads. Stock bridge tag: [kaspaapi.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/bridge/src/kaspaapi.rs) lines 24–55. |
| Header version | 2 on all 1,406 | Verified | Block headers. |
| P2P user agent | Not observed | Unverified | Not in the desk peer list. The stock user agent is built from the same `version()` ([version.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/protocol/p2p/src/convert/model/version.rs)). |
| Protocol version | Not observed | Unverified | Stock `v2.1.0` advertises 11 and accepts 10 ([flow_context.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/protocol/flows/src/flow_context.rs)). The desk's 8 peers were protocol 10 (4) and 11 (4). None matched this label. |
| Services flags | Not observed | Unverified | `RpcPeerInfo` has no services field. Stock `Version::new` sets services to 0. |

## When it was seen

| Item | Result | Status | Source |
|---|---|---|---|
| First labeled block in the retained DAG | 14 Oct 2026 23:54:49.706 CEST | Verified | Desk node. Zero labeled blocks from the pruning point (15:18 the same day) until this one. |
| Last labeled block | 15 Oct 2026 00:56:45.706 CEST | Verified | Desk node. |
| Hours | 14 Oct 23:00 CEST: 71 blocks. 15 Oct 00:00 CEST: 1,335 blocks. Every other retained hour, and the catch-up after 22:09, is 0. | Verified | Desk node. |
| Before the pruning point | Unknown | Unverified | The node does not keep those blocks. |
| After 22:40 CEST | No labeled block in the 18,972 blocks walked after the 22:09 sink | Verified for that catch-up | Desk node at 22:40 CEST. |
| First and last seen on tn10.kaspa.stream | The page listed the miner in stp's screenshot at 14:01 CEST | Unverified as a site timestamp | Screenshot in https://x.com/StppStp/status/2110702638513975733. The live miners and nodes tables did not render in a headless load this session. The site can remember a miner after its blocks stop. |

## Blocks, color, parents

| Item | Result | Status | Source |
|---|---|---|---|
| Labeled blocks stored | 1,406 | Verified | Desk node, full bodies. |
| Blue / red | 1 blue, 1,405 red (99.929%) | Verified | `getCurrentBlockColor`, no errors. |
| Where the colors sit | The blue block is the first one, 14 Oct 23:54:49.706 CEST. All 1,335 blocks in the 00:00 hour are red. | Verified | Same call, grouped by header time. |
| Selected chain | The blue block has `isChainBlock` false and `isHeaderOnly` false. It has 61 parents, none of them labeled, 2 transactions, mergeset 1 blue and 0 red. | Verified | `getBlock` verbose data on that block. |
| Selected parent | 1,404 of 1,406 use a labeled block as the first parent. The first block does not. One later block is the other exception. | Verified | Parent hashes compared with the labeled set. Hashes are not written out. |
| Parent width | Mean 79.7 parents, of which mean 27.5 are earlier labeled blocks. No block has only labeled parents. Minimum 61, maximum 153. | Verified | Same parent lists. |
| Reading | These blocks reference a wide tip set and then walk their own first-parent chain. That is a side chain beside the public DAG. The public chain did not follow it, so the blocks are red. | Verified pattern, interpretation of GHOSTDAG | Colors plus parent counts. |

## The one state split

Across the whole retained DAG, 279,920 pairs of stored blocks shared a 64-bit parent-set prefix. 279,919 of those pairs had the same UTXO commitment and the same accepted-id merkle root. One pair did not. A 64-bit prefix can collide, so that one pair was checked again on the full parent-set hash.

A second walk compared the full parent-set hash for every labeled block against other blocks in the burst window:

| Comparison | Count | UTXO and accepted-id | Status |
|---|---|---|---|
| Labeled block against another block with the same parents | 287 | 286 equal, 1 different | Verified |
| Two labeled blocks with the same parents | 142 | 142 equal on UTXO, accepted-id, and coinbase sum. 2 of them differ in the transaction merkle root only. | Verified |

The different pair:

| Field | Labeled block | Other block | Status |
|---|---|---|---|
| Time | 14 Oct 2026 23:55:01.707 CEST | 14 Oct 2026 23:54:50.154 CEST | Verified |
| Color | Red, not a chain block, full body | Not labeled. Color was not queried. | Verified for the labeled side |
| Parents | 78, full parent-set hash equal to the other block. 17 of the 78 are labeled. | 78, same set | Verified |
| Header version and bits | 2, and the same bits value | 2, same bits | Verified |
| Transactions | 51 | 1 (coinbase only) | Verified |
| UTXO commitment | Differs | Differs | Verified |
| Accepted-id merkle root | Differs | Differs | Verified |
| Transaction merkle root | Differs | Differs | Verified. Expected, because the transaction lists differ. |
| Coinbase output sum | 3,000,000 sompi higher than the other block | 3,000,000 sompi lower | Verified. Amounts themselves are not printed. |

The labeled block's mergeset is 2 blue blocks and 0 red blocks:

1. A stock `2.1.0` block at 14 Oct 23:54:49.928 CEST, coinbase only.
2. The first labeled block, at 14 Oct 23:54:49.706 CEST. It has one non-coinbase transaction. That is the only non-coinbase transaction in the mergeset.

That transaction, **verified** from its body, with no ids written:

- Version 0, lock time 0, empty payload, 1 input, 2 outputs. The node reports mass 488,424.
- Output scripts: one 35-byte script-hash shape (`OP_BLAKE2B`, a 32-byte push, `OP_EQUAL`) and one 34-byte pubkey shape ending in `OP_CHECKSIG`. Neither output is `OP_RETURN`. Opcode numbers: `OP_BLAKE2B` is `0xaa`, `OP_RETURN` is `0x6a`, `OP_CHECKSIG` is `0xac` ([wasm/opcodes.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/wasm/opcodes.rs)). The script-hash template is [standard.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/standard.rs).
- The signature script is 13,653 bytes. At the top level it has no `OP_RETURN` and no `OP_CHECKSIG`. The largest push is 13,080 bytes. Parsed as a script, that push contains 1 `OP_RETURN`, 3 `OP_CHECKSIG`, 4 `OP_BLAKE2B`, and 397 `OP_TRUE`.
- Stock execution of `OP_RETURN` returns `EarlyReturn` ([opcodes/mod.rs](https://github.com/kaspanet/rusty-kaspa/blob/master/crypto/txscript/src/opcodes/mod.rs)). This pass did not run the script, so reaching that opcode is **unverified**.
- The spent output's amount was not read, so "the fee is 3,000,000 sompi" is **unverified**. It is the only non-coinbase transaction in the mergeset of the block whose coinbase is 3,000,000 sompi higher. Coinbase outputs pay the mergeset, subsidy plus fees ([coinbase.rs](https://github.com/kaspanet/rusty-kaspa/blob/v2.1.0/consensus/src/processes/coinbase.rs) lines 109–127).

No other labeled block in the retained DAG shows this split. The other 1,405 blocks are not a stream of conflicting commitments. They are red because their first parent is their own side chain.

## Hashrate and the wasted share

Network hashrate here is `difficulty * 2 * 10`, the formula in [kaspa-rest-server get_hashrate.py](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_hashrate.py).

| Item | Result | Status | Source |
|---|---|---|---|
| Desk difficulty during the color pass | 1,798,474 | Verified | `getBlockDagInfo` on the desk node. Hashrate about 36.0 MH/s. |
| Public TN10 difficulty the same evening | 1,809,127 | Verified | https://api-tn10.kaspa.org/info/blockdag . Hashrate about 36.2 MH/s. |
| Burst length | 3,716 seconds, 14 Oct 23:54:49.706 CEST through 15 Oct 00:56:45.706 CEST | Verified | Header times. |
| Blocks in the burst | 1,406, which is 0.378 blocks/s | Verified | Count over 3,716 seconds. |
| Stored blocks around it | 37,270 in the 23:00 hour and 37,238 in the 00:00 hour, about 10.35 blocks/s | Verified | Same walk. |
| Share during the burst | About 3.7% of stored blocks, about 1.3 MH/s | Verified arithmetic from the rows above | |
| After 00:56:45 CEST | 0 labeled blocks through 15 Oct 23:03:22 CEST | Verified | Desk node. The 22:40 catch-up was the earlier check. A later walk of 1,142,659 stored blocks, from the pruning point through that time, still has 1,406 labeled blocks. |
| Off the selected chain | 1,405 of 1,406 stored blocks are red | Verified | `getCurrentBlockColor`. |

The red blocks' proof of work is off the selected chain. Under the coinbase rules, a red block's subsidy is paid to the miner who merges it, not to the red block's own payload script. This pass did not count how many of the 1,405 were actually merged. That count is **unverified**.

The screenshot's 40.3 H/s and 0.04 blocks/h were read from the image, not from the live page, so those digits stay **unverified**. They cannot describe this burst. At 14:01 CEST the last stored labeled block was already about 13 hours old. A short window after the miner stopped can show a near-zero rate while the all-time red ratio stays at 99.93%.

## Payout and reachability

| Item | Result | Status | Source |
|---|---|---|---|
| Payout scripts in the coinbase payload | 1 distinct script, present on all 1,406 blocks | Verified | Hash of the payload script. The script and the address are not written. |
| Scripts among coinbase outputs | 9 distinct | Verified | Those outputs pay the mergeset, so they include other miners' scripts. They are not 9 addresses of this miner. |
| IP, country, ASN, host | Not known | Unverified | The peer list had no matching user agent. No address was looked up. |
| Inbound p2p | Not tested | Unverified | The desk node listens on loopback and had 8 outbound peers. Absence from that list does not show that the miner refuses inbound connections. |
| RPC | Not tested | Unverified | No second port was probed. The allowed single TCP connect was not used, because the miner was not already a peer. |
| Public DNS seeder | Not queried | Unverified | A seeder answer would be an address list. It was left unread. |

## Trail and timeline

Times in CEST.

| When | What | Status |
|---|---|---|
| 14 Oct 14:30 | [#1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) lowio merged. No version bump. | Verified, GitHub |
| 14 Oct 18:12 | [#1074](https://github.com/kaspanet/rusty-kaspa/pull/1074) merged. Wallet mass. No version bump. | Verified, GitHub. Missing from NODE-LOAD-WORK.md. |
| 14 Oct 23:54:49 | First labeled block. | Verified, desk node |
| 15 Oct 00:07 | [#1150](https://github.com/kaspanet/rusty-kaspa/pull/1150) opened. The label was already on chain. | Verified, GitHub |
| 15 Oct 00:56:45 | Last labeled block. | Verified, desk node |
| 15 Oct 14:01 | stp posts the miners-page screenshot. https://x.com/StppStp/status/2110702638513975733 | Verified, X |
| 15 Oct 17:34 | [#991](https://github.com/kaspanet/rusty-kaspa/pull/991) merged (`7cc22fea`). After the burst. Version still 2.1.0. | Verified, GitHub. Missing from NODE-LOAD-WORK.md. |
| 15 Oct 18:54 | vertex describes the pair. https://x.com/KaspaScopio/status/2110776347572441560 | Verified, X |
| 15 Oct 22:40 | Catch-up still shows no new labeled block. | Verified, desk node |
| 15 Oct 23:03 | Walk through the sink still shows 1,406 labeled blocks, last one 00:56:45. | Verified, desk node |

No public fork, branch, or commit containing `opreturn-era-fix` or `tn10-local` was found. Discord was not searched (**unverified**).

## Share, and how old the payout is

15 Oct 2026, 23:25 CEST. Same desk node. Stored blocks from the pruning point 14 Oct 15:18:14.711 CEST through 15 Oct 23:03:22.876 CEST. 1,142,659 blocks. Nine coinbase-payload scripts. The labeled version is one script, and it is the script that was not among the eight already mining before 14 Oct 23:54.

The question was whether a fall in supertypo's share is this miner, and whether the payout is about two days old. supertypo has said the miners page is built from coinbase-payload text miners set themselves ([11 Oct 2026](https://x.com/supertypo_kas/status/2109220014423216271)). This pass counted that text. It did not read the kaspa.stream share chart.

| Item | Result | Status | Source |
|---|---|---|---|
| Extra containing `supertypo` | One script. The extra is `2.1.0/0.2.6/supertypo`. 5,395 stored blocks. | Verified | Desk node. |
| Same payout script as the labeled miner | No. No script carries both extras. | Verified | Hash of each payload script. The scripts are not written. |
| Stored rate of that `supertypo` extra | About 123 blocks/hour in the full hours 14 Oct 16:00–22:00. 154 in the 23:00 hour, 123 in the 00:00 hour, then about 189 blocks/hour from 15 Oct 01:00 through 22:00. Last block 15 Oct 23:03:19. | Verified | Same walk. |
| Fit to the burst | The burst is about 1,400 labeled blocks in one hour. The `supertypo` extra stays near 120–300 blocks/hour and does not drop. | Verified arithmetic | The rates are different scripts and different sizes. |
| First indexed tx of the labeled payout | 13 Oct 2026 01:11:16 CEST. 22 transactions. Newest indexed tx 14 Oct 23:54:50.154 CEST. | Verified | `transactions-count` and `full-transactions-page?limit=1&after=1` on https://api-tn10.kaspa.org . The older-page header was absent. Code: [get_address_transactions.py](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions.py), [get_address_transactions_count.py](https://github.com/kaspa-ng/kaspa-rest-server/blob/main/endpoints/get_address_transactions_count.py). |
| Age | 46 hours 44 minutes from the first indexed tx (13 Oct 01:11:16.448 CEST) to the first labeled block (14 Oct 23:54:49.706 CEST). The earlier "about 47 hours" was that gap, rounded. 38 hours 7 minutes of it, through the pruning point at 14 Oct 15:18:14.711 CEST, are already outside this node's stored blocks. The remaining 8 hours 37 minutes are still in the window, before the label. | Verified times | The 22 indexed transactions are the indexer's receipts. This node cannot show the part before its pruning point. |
| `supertypo` extra, indexer oldest | 8 Oct 2026 22:59:01 CEST, 166,349 transactions | Verified as an indexer row | The same second appears for another large script. Treat it as the indexer window, not a birth date. |
| Public name | HTTP 500 from `/name` | Unverified | The lookup failed. |
| Peers | 8 peers. No user agent contained `opret`, `supertypo`, or `+`. | Verified for this list | `getConnectedPeerInfo`. No TCP connect. |
| Miners page at 23:25 | Not reloaded | Unverified | The chain has no labeled block after 00:56:45. That fits a row that has stopped. It does not prove what the page drew. |
