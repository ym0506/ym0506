# Selected Engineering Work

[한국어](./README.ko.md)

## At a Glance

- **Apache ShardingSphere:** [10 merged PRs](./SHARDINGSPHERE_CONTRIBUTIONS.md) spanning parser, SQL federation, sharding, metadata, CI reliability, regression coverage, and release documentation.
- **RouteContract:** [v0.1.2](https://github.com/ym0506/routecontract/releases/tag/v0.1.2) with **52 exact-tag tests**, **160 deterministic capture runs**, and **12 inspectable release assets**.

## Featured Project: RouteContract

**A CI contract for detecting ShardingSphere-JDBC physical execution drift even when business results stay unchanged.**

Its real-MySQL fixture preserves the same business result while changing hook-reported JDBC attempts and data-source aliases from **1 to 2**. RouteContract captures that structural difference in a value-minimized manifest and rejects unapproved changes with stable diagnostics.

`1 → 2 observed attempts` · `160 deterministic captures` · `52 tests at v0.1.2`

[Repository](https://github.com/ym0506/routecontract) · [2m 54s demo](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [Release](https://github.com/ym0506/routecontract/releases/tag/v0.1.2) · [Specification](https://github.com/ym0506/routecontract/blob/main/docs/specification.md)

The contract deliberately covers hook-reported execution attempts, not complete route plans or transaction outcomes. The [specification](https://github.com/ym0506/routecontract/blob/main/docs/specification.md) defines the exact claim boundary.

## Selected Apache ShardingSphere Contributions

### [#38449 · Sharding index-name correctness and compatibility](https://github.com/apache/shardingsphere/pull/38449)

A string-length bug exposed a metadata identity and compatibility problem. The fix preserves legacy physical names when safe, adds deterministic length-safe fallbacks, and recovers logical names across DDL rewrite and metadata refresh paths, including new `CREATE TABLE`.

**Scope:** `36 files` · `12 commits` · `5 maintainer change-request rounds` · `87 passing checks`

### [#38659 · PostgreSQL/openGauss window-aggregate cardinality](https://github.com/apache/shardingsphere/pull/38659)

Lost `OVER` metadata made the merge layer synthesize a false aggregate row. Preserving window semantics through parser and binder paths restored the empty-input result from an incorrect **one `NULL` row to zero rows**.

**Scope:** `parser → binder → merge` · `10 files` · `2 dialects` · `79 passing checks`

### [#38405 · Correlated-subquery planner correctness](https://github.com/apache/shardingsphere/pull/38405)

Blocked correlated outer references from being pushed into `LogicalScan` while preserving normal non-correlated project pushdown.

**Verification:** `114 targeted tests` · `shared E2E across MySQL, PostgreSQL, and openGauss` · `141 passing checks`

**Also:** [#38327 · HASH_MOD upgrade compatibility](https://github.com/apache/shardingsphere/pull/38327) · [View all 10 merged contributions](./SHARDINGSPHERE_CONTRIBUTIONS.md)

<sub>Counts verified on 2026-09-03. GitHub check counts are check runs, not test-case counts.</sub>
