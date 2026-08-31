# Selected Engineering Work

[한국어](./README.ko.md)

| Apache ShardingSphere upstream | RouteContract release |
|:---:|:---:|
| **10 merged PRs** | **v0.1.2** |
| 6 production-code PRs · 1 CI reliability PR · 1 regression-coverage PR · 2 docs/release PRs | 7 suites · 52 exact-tag tests · 12 public release assets |

## Apache ShardingSphere

| Contribution and result | Evidence of scope and verification |
|---|---|
| [**#38449: Sharding index-name correctness and compatibility**](https://github.com/apache/shardingsphere/pull/38449)<br><br>A string-length bug exposed a metadata identity and compatibility problem. The fix keeps legacy physical names when safe, uses deterministic length-safe fallbacks, and recovers logical names across DDL rewrite and metadata refresh paths, including new `CREATE TABLE`. | **36 files**<br>**12 commits**<br>**5 maintainer change-request rounds**<br>**87 passing checks** |
| [**#38659: PostgreSQL/openGauss window-aggregate cardinality**](https://github.com/apache/shardingsphere/pull/38659)<br><br>Lost `OVER` metadata made the merge layer synthesize a false aggregate row. Preserving window semantics through parser and binder paths restored the empty-input result from an incorrect **one `NULL` row to zero rows**. | Parser → binder → merge regression<br>**10 files** · **2 dialects**<br>**79 passing checks** |
| [**#38405: Correlated-subquery planner correctness**](https://github.com/apache/shardingsphere/pull/38405)<br><br>Blocked correlated outer references from being pushed into `LogicalScan`, while preserving normal non-correlated project pushdown. | **114 targeted tests**<br>Shared E2E for MySQL, PostgreSQL, and openGauss<br>**141 passing checks** |
| [**#38327: HASH_MOD upgrade compatibility**](https://github.com/apache/shardingsphere/pull/38327)<br><br>Added opt-in normalization for equal negative values across `Integer`, `Long`, and `BigInteger`, while keeping legacy routing as the default to avoid moving existing data across shards after an upgrade. | Legacy and normalized modes<br>Boundary and out-of-range coverage<br>**82 passing checks** |

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
