Nyx Core version 2.0.0
========================

Release is now available from:

  <https://www.nyxcoin.org/download>

This is a new major version release, bringing new features and other improvements.

Please report bugs using the issue tracker at github:

  <https://github.com/nyxpay/nyx/issues>

Upgrading and downgrading
=========================

How to Upgrade
--------------

If you are running an older version, shut it down. Wait until it has completely
shut down (which might take a few minutes for older versions), then run the
installer (on Windows) or just copy over /Applications/Nyx-Qt (on Mac) or
nyxd/nyx-qt (on Linux).

Downgrade warning
-----------------
### Downgrade to a version < 2.0.0

Because release 2.0.0 includes DIP0001 (2 MB block size hardfork) plus
a transaction fee reduction and a fix for masternode rank calculation algo
(which activation also depends on DIP0001) this release will not be
backwards compatible after DIP0001 lock in/activation happens.

This does not affect wallet forward or backward compatibility.

Notable changes
===============

DIP0001
-------

We outline an initial scaling mechanism for Nyx. After deployment and activation, Nyx will be able to handle double the transactions it can currently handle. Together with the faster block times, Nyx we will be prepared to handle eight times the traffic of Bitcoin.

https://github.com/nyxpay/dips/blob/master/dip-0001.md


Fee reduction
-------------

All transaction fees are reduced 10x (from 10K per Kb to 1K per Kb), including fees for InstantSend (from 0.001 NYX per input to 0.0001 per input)

InstantSend fix
---------------

The potential vulnerability found by Matt Robertson and Alexander Block was fixed in d7a8489f3 (#1620).

RPC changes
-----------

There are few changes in existing RPC in this release:
- There is no more `bcconfirmations` field in RPC output and `confirmations` shows blockchain only confirmations by default now. You can change this behaviour by switching new `addlockconf` param to `true`. There is a new rpc field `instantlock` which indicates whether a given transaction is locked via InstantSend. 
- `gobject list` and `gobject diff` accept `funding`, `delete` and `endorsed` filtering options now, in addition to `valid` and `all` currently available;
- `vin` field in `masternode` commands is renamed to `outpoint` and shows data in short format now;
- `getblocktemplate` output is extended with versionbits-related information;
- Output of wallet-related commands `validateaddress` is extended with optional `hdkeypath` and `hdchainid` fields.

There are few new RPC commands also:
- `masternodelist info` shows additional information about sentinel for each masternode in the list;
- `masternodelist pubkey` shows pubkey corresponding to masternodeprivkey for each masternode in the list;
- `gobject check` allows to check proposal data for correctness before preparing/submitting the proposal, `gobject prepare` and `gobject submit` should also perform additional validation now though;
- `setnetworkactive` allows to turn all network activity on and off;
- `dumphdinfo` displays some information about HD wallet (if available).

Command-line options
--------------------

New: `assumevalid`, `blocksonly`, `reindex-chainstate`

Experimental: `usehd`, `mnemonic`, `mnemonicpassphrase`, `hdseed`

See `Help -> Command-line options` in Qt wallet or `nyxd --help` for more info.

PrivateSend improvements
------------------------

Algorithm for selecting inputs was slightly changed in [`6067896ae`](https://github.com/nyxpay/nyx/commit/6067896ae) ([#1248](https://github.com/nyxpay/nyx/pull/1248)). This should allow user to get some mixed funds much faster.

Lots of backports, refactoring and bug fixes
--------------------------------------------

We backported some performance improvements from Bitcoin Core and aligned our codebase with their source a little bit better. We still do not have all the improvements so this work is going to be continued in next releases.

A lot of refactoring and other fixes should make code more reliable and easier to review now.

Experimental HD wallet
----------------------

This release includes experimental implementation of BIP39/BIP44 compatible HD wallet. Wallet type (HD or non-HD) is selected when wallet is created via `usehd` command-line option, default is `0` which means that a regular non-deterministic wallet is going to be used. If you decide to use HD wallet, you can also specify BIP39 mnemonic and mnemonic passphrase (see `mnemonic` and `mnemonicpassphrase` command-line options) but you can do so only on initial wallet creation and can't change these afterwards. If you don't specify them, mnemonic is going to be generated randomly and mnemonic passphrase is going to be just a blank string.

**WARNING:** The way it's currently implemented is NOT safe and is NOT recommended to use on mainnet. Wallet is created unencrypted with mnemonic stored inside, so even if you encrypt it later there will be a short period of time when mnemonic is stored in plain text. This issue will be addressed in future releases.

2.0.0 Change log
=================

### Backports:
- Align with btc 0.12 (#1409)
- Fix for nyx-qt issue with startup and multiple monitors. (#1461)
- Force to use C++11 mode for compilation (#1463)
- Make strWalletFile const (#1459)
- Access WorkQueue::running only within the cs lock. (#1460)
- trivial: fix bloom filter init to isEmpty = true (#1458)
- Avoid ugly exception in log on unknown inv type (#1457)
- Don't return the address of a P2SH of a P2SH (#1455)
- Generate auth cookie in hex instead of base64 (#1454)
- Do not shadow LOCK's criticalblock variable for LOCK inside LOCK (#1453)
- in getrawpmempool (#1452)
- Remove duplicate bantablemodel.h include (#1446)
- Fix for build on Ubuntu 14.04 with system libraries (#1467)
- Improve EncodeBase58/DecodeBase58 performance (#1456)
- auto_ptr → unique_ptr
- Boost 1.61.0
- Boost 1.63.0
- Minimal fix to slow prevector tests as stopgap measure
- build: fix qt5.7 build under macOS (#1469)
- Increase minimum debug.log size to 10MB after shrink. (#1480)
- Backport Bitcoin PRs #6589, #7180 and remaining part of #7181: enable per-command byte counters in `CNode` (#1496)
- Increase test coverage for addrman and addrinfo (#1497)
- Eliminate unnecessary call to CheckBlock (#1498)
- Backport Bincoin PR#7348: MOVE ONLY: move rpc* to rpc/ + same for Nyx-specific rpc (#1502)
- Backport Bitcoin PR#7349: Build against system UniValue when available (#1503)
- Backport Bitcoin PR#7350: Banlist updates (#1505)
- Backport Bitcoin PR#7458: [Net] peers.dat, banlist.dat recreated when missing (#1506)
- Backport Bitcoin PR#7696: Fix de-serialization bug where AddrMan is corrupted after exception (#1507)
- Backport Bitcoin PR#7749: Enforce expected outbound services (#1508)
- Backport Bitcoin PR#7917: Optimize reindex (#1515)
- Remove non-determinism which is breaking net_tests #8069 (#1517)
- fix race that could fail to persist a ban (#1518)
- Backport Bitcoin PR#7906: net: prerequisites for p2p encapsulation changes (#1521)
- Backport Bitcoin PR#8084: Add recently accepted blocks and txn to AttemptToEvictConnection (#1522)
- Backport Bitcoin PR#8113: Rework addnode behaviour (#1525)
- Remove bad chain alert partition check (#1529)
- Added feeler connections increasing good addrs in the tried table. (#1530)
- and other cs_main locking fixes (#1535)
- Backport Bitcoin PR#8085: p2p: Begin encapsulation (#1537)
- Backport Bitcoin PR#8049: Expose information on whether transaction relay is enabled in `getnetwork` (#1545)
- (#1548)
- Backport Bitcoin PR#8708: net: have CConnman handle message sending (#1553)
- net: only delete CConnman if it's been created (#1555)
- Backport Bitcoin PR#8865: Decouple peer-processing-logic from block-connection-logic (#1556)
- (#1558)
- (#1560)
- Backport Bitcoin PR#9183: Final Preparation for main.cpp Split (#1561)
- Backport Bitcoin PR#8822: net: Consistent checksum handling (#1565)
- (#1566)
- Backport Bitcoin PR#9289: net: drop boost::thread_group (#1568)
- + Nyxify
- Backport Bitcoin PR#9441: Net: Massive speedup. Net locks overhaul (#1586)
- Backport "assumed valid blocks" feature from Bitcoin 0.13 (#1582)
- Partially backport Bitcoin PR#9626: Clean up a few CConnman cs_vNodes/CNode things (#1591)
- Do not add random inbound peers to addrman. (#1593)
- net: No longer send local address in addrMe (#1600)
- Backport Bitcoin PR#7868: net: Split DNS resolving functionality out of net structures (#1601)
- Backport Bitcoin PR#8128: Net: Turn net structures into dumb storage classes (#1604)
- Backport Bitcoin Qt/Gui changes up to 0.14.x part 1 (#1614)
- Backport Bitcoin Qt/Gui changes up to 0.14.x part 2 (#1615)
- Backport Bitcoin Qt/Gui changes up to 0.14.x part 3 (#1617)
- Merge #8944: Remove bogus assert on number of oubound connections. (#1685)
- Merge #10231: [Qt] Reduce a significant cs_main lock freeze (#1704)
- Backport #7056: Save last db read
- Backport #7756: Add cursor to iterate over utxo set, use this in `gettxoutsetinfo`
- Backport #7904: txdb: Fix assert crash in new UTXO set cursor
- Backport #7927: Minor changes to dbwrapper to simplify support for other databases
- Backport #7815: Break circular dependency main ↔ txdb
- Move index structures into spentindex.h
- Add SipHash-2-4 primitives to hash
- Use SipHash-2-4 for CCoinsCache index
- Use C++11 thread-safe static initializers in coins.h/coins.cpp
- Backport #7874: Improve AlreadyHave
- Backport #7933: Fix OOM when deserializing UTXO entries with invalid length
- Backport #8273: Bump `-dbcache` default to 300MiB
- Backport #8467: [Trivial] Do not shadow members in dbwrapper
- Use fixed preallocation instead of costly GetSerializeSize
- Backport #9307: Remove undefined FetchCoins method declaration
- Backport #9346: Batch construct batches
- Backport #9308: [test] Add CCoinsViewCache Access/Modify/Write tests
- Backport #9107: Safer modify new coins
- Backport #9310: Assert FRESH validity in CCoinsViewCache::BatchWrite
- Backport #8610: Share unused mempool memory with coincache
- method to CDataStream (and use it)
- Backport #9999: [LevelDB] Plug leveldb logs to bitcoin logs
- Backport #10126: Compensate for memory peak at flush time
- Backport #10133: Clean up calculations of pcoinsTip memory usage
- Make DisconnectBlock and ConnectBlock static in validation.cpp
- Backport #10297: Simplify DisconnectBlock arguments/return value
- Backport #10445: Add test for empty chain and reorg consistency for gettxoutsetinfo.
- Add COMPACTSIZE wrapper similar to VARINT for serialization
- Fix use of missing self.log in blockchain.py
- Backport #10250: Fix some empty vector references
- Backport #10249: Switch CCoinsMap from boost to std unordered_map
- Backport #10195: Switch chainstate db and cache to per-txout model
- Fix CCoinsViewCache::GetPriority to use new per-utxo
- Fix address index to use new per-utxo DB
- Nyx related fixes for per-utxo DB
- Backport #10550: Don't return stale data from CCoinsViewCache::Cursor()
- Backport #10537: Few Minor per-utxo assert-semantics re-adds and tweak
- Backport #10559: Change semantics of HaveCoinInCache to match HaveCoin
- Backport #10581: Simplify return values of GetCoin/HaveCoin(InCache)
- Backport #10558: Address nits from per-utxo change
- Backport #10660: Allow to cancel the txdb upgrade via splashscreen keypress 'q'
- Backport #10526: Force on-the-fly compaction during pertxout upgrade
- Backport #10985: Add undocumented -forcecompactdb to force LevelDB compactions
- Backport #10998: Fix upgrade cancel warnings
- Backport #11529: Avoid slow transaction search with txindex enabled
- build: quiet annoying warnings without adding new ones
- build: silence gcc7's implicit fallthrough warning

### Masternodes:
- Remove support for local masternodes (#1706)

### PrivateSend:
- mix inputs with highest number of rounds first (#1248)
- Few fixes for PrivateSend (#1408)
- Refactor PS (#1437)
- PrivateSend: dont waste keys from keypool on failure in CreateDenominated (#1473)
- anonymizable balance (#1477)
- split CPrivateSend (#1492)
- Expire confirmed DSTXes after ~1h since confirmation (#1499)
- fix MakeCollateralAmounts (#1500)
- fix a bug in CommitFinalTransaction (#1540)
- fix number of blocks to wait after successful mixing tx (#1597)
- Fix losing keys on PrivateSend (#1616)
- Make sure mixing masternode follows bip69 before signing final mixing tx (#1510)
- speedup MakeCollateralAmounts by skiping denominated inputs early (#1631)
- Keep track of wallet UTXOs and use them for PS balances and rounds calculations (#1655)
- do not calculate stuff that are not going to be visible in simple PS UI anyway (#1656)
- Refactor PrivateSend (#1735)
- private, execute it on both client and server (#1736)

### InstantSend:
- Safety check in CInstantSend::SyncTransaction (#1412)
- Fix potential deadlock in CInstantSend::UpdateLockedTransaction (#1571)
- fix instantsendtoaddress param convertion (#1585)
- Fix: Reject invalid instantsend transaction (#1583)
- InstandSend overhaul (#1592)
- fix SPORK_5_INSTANTSEND_MAX_VALUE validation in CWallet::CreateTransaction (#1619)
- Fix masternode score/rank calculations (#1620)
- fix instantsend-related RPC output (#1628)
- bump MIN_INSTANTSEND_PROTO_VERSION to 70208 (#1650)
- (#1695)
- remove InstantSend votes for failed lock attemts after some timeout (#1705)
- update setAskFor on TXLOCKVOTE (#1713)
- fix bug introduced in #1695 (#1714)
- Allow IS for all txes, not only for txes with p2pkh and data outputs (#1760)
- InstantSend txes should never qualify to be a 0-fee txes (#1777)

### Governance:
- Few changes for governance rpc: (#1351)
- sentinel uses status of funding votes (#1440)
- Validate proposals on prepare and submit (#1488)
- Replace watchdogs with ping (#1491)
- Fixed issues with propagation of governance objects (#1489)
- Fix issues with mapSeenGovernanceObjects (#1511)
- change invalid version string constant (#1532)
- Fix vulnerability with mapMasternodeOrphanObjects (#1512)
- New rpc call "masternodelist info" (#1513)
- add 6 to strAllowedChars (#1542)
- fixed potential deadlock in CSuperblockManager::IsSuperblockTriggered (#1536)
- (#1538)
- fix potential deadlock in CGovernanceManager::ProcessVote (#1541)
- fix potential deadlock in CMasternodeMan::CheckMnbAndUpdateMasternodeList (#1543)
- Fix MasternodeRateCheck (#1490)
- fix off-by-1 in CSuperblock::GetPaymentsLimit (#1598)
- Relay govobj and govvote to every compatible peer, not only to the one with the same version (#1662)
- allow up to 40 chars in proposal name (#1693)
- start_epoch, end_epoch and payment_amount should be numbers, not strings (#1707)

### Network/Sync:
- fix sync reset which is triggered erroneously during reindex (#1478)
- track asset sync time (#1479)
- do not use masternode connections in feeler logic (#1533)
- Make sure mixing messages are relayed/accepted properly (#1547)
- fix CDSNotificationInterface::UpdatedBlockTip signature (#1562)
- Sync overhaul (#1564)
- limit UpdatedBlockTip in IBD (#1570)
- Relay tx in sendrawtransaction according to its inv.type (#1584)
- for inactivity checks (#1588)
- Use GetAdjustedTime instead of GetTime when dealing with network-wide timestamps (#1590)
- Fix sync issues (#1599)
- Fix duplicate headers download in initial sync (#1589)
- instead of g_connman global. (#1610)
- Eliminate g_connman use in spork module. (#1613)
- Eliminate g_connman use in instantx module. (#1626)
- CMasternodeSync log messages to new `mnsync` log category (#1630)
- Eliminate remaining uses of g_connman in Nyx-specific code. (#1635)
- Wait for full sync in functional tests that use getblocktemplate. (#1642)
- fix sync (#1643)
- (#1654)
- Remove cs_main from ThreadMnbRequestConnections (#1658)
- Fix bug: nCachedBlockHeight was not updated on start (#1673)
- Add more logging for MN votes and MNs missing votes (#1683)
- Fix sync reset on lack of activity (#1686)
- Fix mnp relay bug (#1700)
- Make sure to clear setAskFor in Nyx submodules (#1730)
- fine-tune sync conditions in getblocktemplate (#1739)
- Bump MIN_PEER_PROTO_VERSION to 70208 (#1772)
- Fix mnp and mnv invs (#1775)
- Improve sync (#1779)
- Fix ProcessVerifyBroadcast (#1780)

### Build:
- Allow compilation with `--disable-wallet` (#1733)
- Show test progress for tests running in wine to avoid Travis timeout (#1740)
- Adjust tests to avoid Travis timeouts (#1745)
- Force rcc to use resource format version 1. (#1784)

### GUI:
- Full path in "failed to load cache" warnings (#1411)
- Qt: bug fixes and enhancement to traffic graph widget  (#1429)
- Icon Cutoff Fix (#1485)
- Fix windows installer script, should handle `nyx:` uri correctly now (#1550)
- Update startup shortcuts (#1551)
- fix TrafficGraphData bandwidth calculation (#1618)
- Fix empty tooltip during sync under specific conditions (#1637)
- [GUI] Change look of modaloverlay (#1653)
- Fix compilation with qt < 5.2 (#1672)
- Translations201710 - en, de, fi, fr, ru, vi (#1659)
- Translations 201710 part2 (#1676)
- Add hires version of `light` theme for Hi-DPI screens (#1712)
- Fix traditional UI theme (#1741)
- Fix ru typo (#1742)

### DIP0001:
- DIP0001 implementation (#1594)
- (#1621)
- fix fDIP0001* flags initialization (#1625)
- fix DIP0001 implementation (#1639)
- fix: update DIP0001 related stuff even in IBD (#1652)
- fix: The idea behind fDIP0001LockedInAtTip was to indicate that it WAS locked in, not that it IS locked in (#1666)

### Wallet:
- HD wallet (#1405)
- Minor Warning Fixed (#1482)
- Disable HD wallet by default (#1629)
- Lower tx fees 10x (#1632)
- Ensure Nyx wallets < 0.12.2 can't open HD wallets (#1638)
- fix fallback fee (#1649)
- post-DIP0001 cleanup (#1763)
- Fix WarningBitsConditionChecker (#1765)

### RPC:
- add `masternodelist pubkey` to rpc (#1549)
- more "vin" -> "outpoint" in masternode rpc output (#1633)
- fix `masternode current` rpc (#1640)
- remove send addresses from listreceivedbyaddress output (#1664)
- fix Examples section of the RPC output for listreceivedbyaccount, listreceivedbyaccount and sendfrom commands (#1665)
- RPC help formatting updates (#1670)
- Revert "fix `masternode current` rpc (#1640)" (#1681)
- partially revert "[Trivial] RPC help formatting updates #1670" (#1711)

### Docs:
- Doc: fix broken formatting in markdown #headers (#1462)
- Added clarifications in INSTALL readme for newcomers (#1481)
- Documentation: Add spork message / details to protocol-documentation.md (#1493)
- Documentation: Update undocumented messages in protocol-documentation.md  (#1596)
- Update `instantsend.md` according to PR#1628 changes (#1663)
- Update build-osx.md formatting (#1690)
- 12.2 release notes (#1675)
- Two small fixes in docs (#1746)
- Fix typo in release-notes.md (#1759)
- [Trivial] Typo/doc updates and RPC help formatting (#1758)
- move 0.12.2 release notes
- Bump version in README.md to 0.12.2 (#1774)
- Clarify usage of pointers and references in code (#1778)

### Other (noticeable) refactoring and fixes:
- Refactor: CDarkSendSigner (#1410)
- Implement BIP69 outside of CTxIn/CTxOut (#1514)
- fix deadlock (#1531)
- fixed potential deadlock in CMasternodePing::SimpleCheck (#1534)
- Masternode classes: Remove repeated/un-needed code and data (#1572)
- add/use GetUTXO\[Coins/Confirmations\] helpers instead of GetInputAge\[IX\] (#1578)
- (#1579)
- drop masternode index (#1580)
- slightly refactor CDSNotificationInterface (#1581)
- safe version of GetMasternodeByRank (#1595)
- Refactor masternode management (#1611)
- Remove some recursive locks (#1624)

### Other (technical) commits:
- bump to 0.12.2.0 (#1407)
- Merge remote-tracking branch 'remotes/origin/master' into v0.12.2.x
- Merge pull request #1431 from nyxpay/v0.12.2.x-merge_upstream
- (#1415)
- c++11: don't throw from the reverselock destructor (#1421)
- Rename bitcoinconsensus library to nyxconsensus. (#1432)
- Fix the same header included twice. (#1474)
- fix travis ci mac build (#1483)
- fix BIP34 starting blocks for mainnet/testnet (#1476)
- adjust/fix some log and error messages (#1484)
- Nyxify bitcoin unix executables (#1486)
- Don't try to create empty datadir before the real path is known (#1494)
- (#1441)
- various trivial cleanup fixes (#1501)
- include atomic (#1523)
- Revert "fixed regtest+ds issues" (#1524)
- workaround for travis (#1526)
- Pass reference when calling HasPayeeWithVotes (#1569)
- typo: "Writting" -> "Writing" (#1605)
- build: silence gcc7's implicit fallthrough warning (#1622)
- bump MIN_MASTERNODE_PAYMENT_PROTO_VERSION_2 and PROTOCOL_VERSION to 70208 (#1636)
- fix BIP68 granularity and mask (#1641)
- Revert "fix BIP68 granularity and mask (#1641)" (#1647)
- Fork testnet to test 12.2 migration (#1660)
- fork testnet again to re-test dip0001 because of 2 bugs found in 1st attempt (#1667)
- update nMinimumChainWork and defaultAssumeValid for testnet (#1668)
- (#1682)
- masternode on mn start (#1689)
- Fix/optimize images (#1688)
- fix trafficgraphdatatests for qt4 (#1699)
- bump to 0.12.3.0 (#1726)
- Unify GetNextWorkRequired (#1737)
- Spelling mistake in validation.cpp (#1752)
- add `maxgovobjdatasize` field to the output of `getgovernanceinfo` (#1757)
- Drop `IsNormalPaymentScript`, use `IsPayToPublicKeyHash` (#1761)
- De-bump to 0.12.2.2 (#1768)
- Make sure additional indexes are recalculated correctly in VerifyDB (#1773)
- Fix CMasternodeMan::ProcessVerify* logs (#1782)
