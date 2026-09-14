# Apache ShardingSphere Contributions

[한국어](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) · [Back to profile](./README.md) · [All open source contributions](./CONTRIBUTIONS.md)

**11 merged ShardingSphere PRs:** seven runtime code improvements, one CI improvement, one regression-test contribution, and two documentation changes. Including OpenTelemetry and go-mysql-server, **[13 upstream PRs are merged](./CONTRIBUTIONS.md)**. Status checked: 2026-09-14.

## Selected contributions

### Generated index names that respect limits and recover logical names

Fixed generated sharding index names that could exceed database identifier limits. Existing names are retained when they fit; longer names use deterministic hash suffixes and truncation within a UTF-8 byte budget. [PR #38449](https://github.com/apache/shardingsphere/pull/38449)

**Engineering scope: 36 changed files — 23 production Java files and 13 tests or test resources.** The change spans DDL rewriting, metadata refresh, and pipeline paths. These counts describe implementation scope, while the fixture below demonstrates the corrected behavior. [Changed files](https://github.com/apache/shardingsphere/pull/38449/files)

The change also handles metadata recovery after name generation. A newly created table has no existing logical metadata, so the implementation derives name candidates from the parsed `CREATE TABLE` statement and verifies them against the generation rules. Recovery is carried through SQL rewriting, metadata refresh, and pipeline paths.

**PostgreSQL regression example: 85 → 63 UTF-8 bytes.** Applying the old naming formula to the same test input produces 85 bytes; the updated result is asserted at 63 bytes. This measures the length of one fixture's index name. [Fixture and result assertion](https://github.com/apache/shardingsphere/blob/b41c843dfefe41038851d83db5b1e2a47ca88b39/infra/common/src/test/java/org/apache/shardingsphere/infra/metadata/database/schema/util/IndexMetaDataUtilsTest.java#L120-L125)

### Correct empty results for window aggregates

Fixed PostgreSQL/openGauss window aggregate queries that returned **one synthetic NULL row for empty sharded input, restoring the correct zero-row result**. [PR #38659](https://github.com/apache/shardingsphere/pull/38659)

The parser had dropped `OVER` metadata, causing the binder to classify a window aggregate as an ordinary aggregate. The merger then synthesized a row. The fix preserves that metadata through parser and binder paths, with regression coverage for column labels and empty-result merging.

### Correct planning for correlated subqueries

Fixed SQL Federation compilation failures when an `IN` subquery projects an outer query's column. The change prevents expressions containing correlated references from being pushed into a scan. [PR #38405](https://github.com/apache/shardingsphere/pull/38405)

Nested expressions are checked for outer references while ordinary projection pushdown remains available. Coverage includes planner-rule and compiler integration tests plus a SQL E2E fixture targeting MySQL, PostgreSQL, and openGauss.

### Failure notification before compatibility fallback

**Merged September 14, 2026** · [PR #39764](https://github.com/apache/shardingsphere/pull/39764)

While auditing `SQLExecutionHook` completion for RouteContract, I found a path where JDBC execution failed but compatibility recovery returned a result without a terminal hook notification. Moving `finishFailure` before recovery reports the original SQL exception while preserving existing trunk/worker results and ordinary exception propagation. [Discovery and failing-before-fix reproduction](https://github.com/apache/shardingsphere/issues/39763)

**Scope: 2 files — one production file and one test file.** The production change moves one existing callback; this is a targeted execution-state reliability fix.

- **Two execution paths:** strengthened an existing test into trunk/worker parameterized cases that verify return values, the original exception, callback order, and exactly one failure notification. [Regression test](https://github.com/apache/shardingsphere/blob/b13ad32b12ceb92868445c98dcc811dadf39ecc5/infra/executor/src/test/java/org/apache/shardingsphere/infra/executor/sql/execute/engine/driver/jdbc/JDBCExecutorCallbackTest.java#L74-L102)
- **196 executor-suite test executions passed** in recorded upstream CI, including the focused regression cases. This is the full suite count, not 196 new tests. [CI run](https://github.com/apache/shardingsphere/actions/runs/34171532124) · [Maintainer approval](https://github.com/apache/shardingsphere/pull/39764#pullrequestreview-5195469387)

The affected path is heterogeneous-database compatibility recovery in ShardingSphere's executor. Verification is at unit-test level; a dedicated live heterogeneous Proxy/database reproduction was not run. This does not establish the same failure in RouteContract's supported ShardingSphere-JDBC 5.5.3/MySQL environment.

## All merged ShardingSphere PRs

| Category | PR | Result |
|---|---|---|
| Runtime | [#39764](https://github.com/apache/shardingsphere/pull/39764) | Reported SQL execution failure before compatibility fallback while preserving existing trunk/worker return values |
| Runtime | [#38449](https://github.com/apache/shardingsphere/pull/38449) | Enforced generated index-name length budgets and restored logical metadata |
| Runtime | [#38659](https://github.com/apache/shardingsphere/pull/38659) | Restored correct empty results for PostgreSQL/openGauss window aggregates |
| Runtime | [#38405](https://github.com/apache/shardingsphere/pull/38405) | Prevented invalid projection pushdown for correlated IN subqueries |
| Runtime | [#38327](https://github.com/apache/shardingsphere/pull/38327) | Added opt-in HASH_MOD normalization for equal int-range values across **three types**: Integer, Long, and BigInteger. Legacy routing remains the default |
| Runtime | [#38187](https://github.com/apache/shardingsphere/pull/38187) | Preserved PostgreSQL unary NOT in the AST so nested sharding tables can be extracted |
| Runtime | [#38223](https://github.com/apache/shardingsphere/pull/38223) | Added TABLE_SCHEMA filtering to MySQL constraint metadata queries |
| CI | [#38352](https://github.com/apache/shardingsphere/pull/38352) | Reused one source snapshot in downstream E2E jobs and excluded unused docs |
| Regression tests | [#38685](https://github.com/apache/shardingsphere/pull/38685) | Covered encrypt-rule deletion, same-name recreation, and post-drop DML. No runtime code changes |
| Documentation | [#38206](https://github.com/apache/shardingsphere/pull/38206) | Added **six pages** covering three DistSQL commands in English and Chinese |
| Documentation | [#39535](https://github.com/apache/shardingsphere/pull/39535) | Added the missing release note for another contributor's Cartesian routing fix |

## CI archive size

**CI measurement:** In #38352, excluding unused docs from the newly introduced source archive reduced its locally compared size from **about 109 MB to 5.1 MB, approximately 95%**. These are approximate archive-size measurements; workflow duration and failure-rate improvements were not measured. [Public comparison and command](https://github.com/apache/shardingsphere/pull/38352#issuecomment-4011386704)

## Merged contribution to another project

**go-mysql-server:** Implemented `NumberType` for **four system numeric types**, improving numeric-type recognition and interface compatibility. Added compile-time interface assertions and numeric/string type-classification coverage. [Merged PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442)
