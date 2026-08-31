# Selected Engineering Work

[한국어](./README.ko.md)

| Apache ShardingSphere upstream | RouteContract release |
|:---:|:---:|
| **10 merged PRs** | **v0.1.2** |
| 6 production-code PRs · 1 CI reliability PR · 1 regression-coverage PR · 2 docs/release PRs | 7 suites · 52 exact-tag tests · 12 public release assets |

## Apache ShardingSphere

| Contribution | Engineering result | Evidence of scope and verification |
|---|---|---|
| [**#38449: Sharding index-name correctness and compatibility**](https://github.com/apache/shardingsphere/pull/38449) | What looked like a string-length bug required a legacy-compatible naming and recovery contract. The fix keeps legacy physical names when safe, applies deterministic length-safe fallbacks when required, and recovers logical names across DDL rewrite, metadata revision, pipeline, and new `CREATE TABLE` paths. | **36 files** · **12 commits** · **5 maintainer change-request rounds** · **87 passing checks** |
| [**#38659: PostgreSQL/openGauss window-aggregate cardinality**](https://github.com/apache/shardingsphere/pull/38659) | Missing `OVER` metadata in the parser surfaced as a false aggregate row in the merge layer. Preserving window semantics through parser and binder paths changed the reported empty-input result from an incorrect **one `NULL` row to zero rows**. | Parser → binder → merge regression · **10 files** · **2 dialects** · **79 passing checks** |
| [**#38405: Correlated-subquery planner correctness**](https://github.com/apache/shardingsphere/pull/38405) | Prevented a correlated outer reference from being pushed into `LogicalScan`, where scan conversion could not handle it. Non-correlated project pushdown remains unchanged. | **114 targeted tests** · shared E2E for MySQL, PostgreSQL, and openGauss · **141 passing checks** |
| [**#38327: HASH_MOD upgrade compatibility**](https://github.com/apache/shardingsphere/pull/38327) | Equal negative values could route differently when represented as `Integer`, `Long`, or `BigInteger`. Added opt-in numeric normalization while keeping legacy routing as the default to avoid moving existing data across shards after an upgrade. | Legacy and normalized modes · boundary and out-of-range coverage · **82 passing checks** |

[View all 10 merged Apache ShardingSphere contributions](./SHARDINGSPHERE_CONTRIBUTIONS.md)

## RouteContract

[RouteContract](https://github.com/ym0506/routecontract) turns ShardingSphere-JDBC physical execution drift into a reviewable CI contract.

A functional assertion can still return the same business row while hook-reported physical JDBC attempts and data-source aliases change from **1 to 2**. The verified real-MySQL fixture captures that difference as a value-minimized manifest and rejects an unapproved candidate with stable `RCM201` and `RCM202` diagnostics.

| Capability | Verified evidence |
|---|---|
| Capture one named application operation and compare its observed execution structure with a human-approved baseline | Real-MySQL Quick Start: same business result, observed attempts and data sources **1 → 2** |
| Keep structural output deterministic enough for source control and CI review | 8-case corpus × 20 runs = **160 captures**, one structural signature per case |
| Publish a reproducible release boundary | `v0.1.2`: **7 suites, 52 tests, 0 failures/errors/skips** |
| Make release contents inspectable | **12 public assets** including JAR, POM, sources, Javadoc, SBOMs, checksums, and supply-chain evidence |

[Repository](https://github.com/ym0506/routecontract) · [2m 54s demo](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [v0.1.2 release](https://github.com/ym0506/routecontract/releases/tag/v0.1.2) · [Specification](https://github.com/ym0506/routecontract/blob/main/docs/specification.md)

The contract is intentionally scoped to hook-reported execution attempts. Complete route plans and transaction outcomes remain outside its claim boundary; the exact boundary is documented in the specification.

<sub>GitHub check counts above are check runs, not test-case counts. The contribution count is current as of 2026-08-31.</sub>
