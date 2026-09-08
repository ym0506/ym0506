# Java · SQL · Data Correctness

I reproduce SQL failures, trace execution paths, and contribute fixes to Apache ShardingSphere. I also build **RouteContract**, a Java library that checks SQL execution changes in CI.

[한국어](./README.ko.md) · [Open-source contribution details](./SHARDINGSPHERE_CONTRIBUTIONS.md) · [Project evidence](./ROUTECONTRACT.md)

## Apache ShardingSphere — 10 merged contributions

Six runtime code improvements, one CI improvement, one regression-test contribution, and two documentation changes. Selected work:

- **Fixed generated index names that could break DDL and metadata handling.** Added naming rules that respect database limits while retaining legacy names where safe and recovering logical names. [PR #38449](https://github.com/apache/shardingsphere/pull/38449)
- **Corrected an empty-table result from one false NULL row to zero rows.** Preserved PostgreSQL/openGauss window-function metadata through parser and binder paths to fix result merging. [PR #38659](https://github.com/apache/shardingsphere/pull/38659)
- **Fixed execution planning for correlated subqueries.** Blocked unsafe pushdown of expressions containing outer references while retaining ordinary projection optimization. [PR #38405](https://github.com/apache/shardingsphere/pull/38405)

Also: [consistent HASH_MOD routing across numeric types](https://github.com/apache/shardingsphere/pull/38327) · [system numeric-type support in go-mysql-server](https://github.com/dolthub/go-mysql-server/pull/3442).

## RouteContract — detect changed DB execution behind unchanged query results

A Java test library that adds **execution-structure checks** alongside existing result assertions.

- **Problem and result:** A real-MySQL fixture returns the same business result while observed execution attempts increase from **1 to 2**. RouteContract rejects changes that exceed the reviewed baseline in CI.
- **Implementation:** Compares attempt counts, data sources, and rewritten SQL structure, with Markdown and JSON review reports.
- **Release and verification:** **v0.1.3 on Maven Central**, with **62 passing Java/MySQL source tests**. [Conditions and evidence](./ROUTECONTRACT.md)

[Repository / install](https://github.com/ym0506/routecontract) · [2m 54s demo](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [Example CI report](https://github.com/ym0506/routecontract/blob/main/docs/evidence/ci-review-report-example.md)

<sub>Supported: Java 17 · ShardingSphere-JDBC 5.5.3 · synchronous, non-batch PreparedStatement. Attempts are reported by SQLExecutionHook. The 1 → 2 example demonstrates regression detection, not a performance improvement or production throughput. Contribution and release status checked on 2026-09-08.</sub>
