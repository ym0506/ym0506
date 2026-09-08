# Java · SQL Engines · Developer Tools

I contribute SQL engine enhancements to Apache ShardingSphere and build **RouteContract**, a Java library for catching execution regressions in CI.

**[11 merged upstream PRs](./SHARDINGSPHERE_CONTRIBUTIONS.md)** · **[RouteContract on Maven Central](./ROUTECONTRACT.md)**<br>
[한국어](./README.ko.md)

## Apache ShardingSphere — 10 merged PRs

**6 runtime code improvements**, plus CI, regression tests, and documentation.

**Index naming — compatibility enhancement** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
**Scope: 36 changed files**, covering implementation and tests across DDL rewriting, metadata refresh, and pipeline paths.<br>
**Result: 85 → 63 UTF-8 bytes** in a PostgreSQL regression fixture, meeting the database identifier limit. Preserved legacy physical names when safe and enabled logical-name recovery.

**HASH_MOD — new opt-in routing feature** · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
**3 numeric types:** Integer, Long, and BigInteger. Added consistent routing for equal values in the signed 32-bit integer range, while keeping legacy routing as the default for upgrades.

**Window aggregates — SQL correctness fix** · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
**2 SQL dialects · 1 incorrect NULL row → 0 rows.** Preserved PostgreSQL/openGauss window semantics through parser and binder paths to restore correct empty-input results.

Also: [correlated-query planner fix](https://github.com/apache/shardingsphere/pull/38405) · [CI archive: ~109 → 5.1 MB in a local comparison](./SHARDINGSPHERE_CONTRIBUTIONS.md#ci-archive-size)<br>
[All contributions, scope, and evidence](./SHARDINGSPHERE_CONTRIBUTIONS.md) — includes a merged four-type compatibility improvement in go-mysql-server.

## RouteContract — SQL execution regression checks

**Published Java library · v0.1.3 · Maven Central**

- **Regression detected:** a real-MySQL fixture keeps the same business result while observed JDBC execution attempts increase **1 → 2**. RouteContract rejects the change against the reviewed baseline in CI.
- **Library features:** operation capture, execution budgets, rewritten-SQL checks, and Markdown/JSON review reports.
- **Verified results:** **62 Java/MySQL tests passed at v0.1.3**; **160 captures = 8 fixed cases × 20 repetitions** with one structural signature per case.

[Install](https://github.com/ym0506/routecontract#install-013) · [2m 54s demo](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [Report example](https://github.com/ym0506/routecontract/blob/main/docs/evidence/ci-review-report-example.md) · [Test conditions and evidence](./ROUTECONTRACT.md)

<sub>Supported: Java 17 · exact ShardingSphere-JDBC 5.5.3 · synchronous, non-batch PreparedStatement. Execution counts are hook-reported attempts in controlled fixtures. Status checked: 2026-09-08.</sub>
