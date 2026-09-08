# Apache ShardingSphere Contributions

[한국어](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) · [Back to profile](./README.en.md)

**10 merged ShardingSphere PRs:** six runtime code improvements, one CI improvement, one regression-test contribution, and two documentation changes. With one contribution to go-mysql-server, **11 external open-source PRs are merged**. Status checked: 2026-09-08.

## Selected contributions

### Generated index names that respect limits and recover logical names

Fixed generated sharding index names that could exceed database identifier limits. Existing names are retained when they fit; longer names use deterministic hash suffixes and truncation within a UTF-8 byte budget. [PR #38449](https://github.com/apache/shardingsphere/pull/38449)

The change also handles metadata recovery after name generation. A newly created table has no existing logical metadata, so the implementation derives name candidates from the parsed `CREATE TABLE` statement and verifies them against the generation rules. Recovery is carried through SQL rewriting, metadata refresh, and pipeline paths.

**PostgreSQL regression example: 85 → 63 UTF-8 bytes.** Applying the old naming formula to the same test input produces 85 bytes; the updated result is asserted at 63 bytes. This measures the length of one fixture's index name. [Fixture and result assertion](https://github.com/apache/shardingsphere/blob/b41c843dfefe41038851d83db5b1e2a47ca88b39/infra/common/src/test/java/org/apache/shardingsphere/infra/metadata/database/schema/util/IndexMetaDataUtilsTest.java#L120-L125)

### Correct empty results for window aggregates

Fixed PostgreSQL/openGauss window aggregate queries that returned **one synthetic NULL row for empty sharded input, restoring the correct zero-row result**. [PR #38659](https://github.com/apache/shardingsphere/pull/38659)

The parser had dropped `OVER` metadata, causing the binder to classify a window aggregate as an ordinary aggregate. The merger then synthesized a row. The fix preserves that metadata through parser and binder paths, with regression coverage for column labels and empty-result merging.

### Correct planning for correlated subqueries

Fixed SQL Federation compilation failures when an `IN` subquery projects an outer query's column. The change prevents expressions containing correlated references from being pushed into a scan. [PR #38405](https://github.com/apache/shardingsphere/pull/38405)

Nested expressions are checked for outer references while ordinary projection pushdown remains available. Coverage includes planner-rule and compiler integration tests plus a SQL E2E fixture targeting MySQL, PostgreSQL, and openGauss.

## All merged ShardingSphere PRs

| Category | PR | Result |
|---|---|---|
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

**CI measurement:** In #38352, excluding unused docs from the newly introduced source archive reduced its locally compared size from **about 109 MB to 5.1 MB, approximately 95%**. These are approximate archive-size measurements; workflow duration and failure-rate improvements were not measured. [Public comparison and command](https://github.com/apache/shardingsphere/pull/38352#issuecomment-4011386704)

## Merged contribution to another project

**go-mysql-server:** Implemented `NumberType` for **four system numeric types**, improving numeric-type recognition and interface compatibility. Added compile-time interface assertions and numeric/string type-classification coverage. [Merged PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442)

## Investigations and proposals in progress

These items are tracked separately from the 11 merged PRs above.

| Item | Current status and scope |
|---|---|
| [ShardingSphere #39763](https://github.com/apache/shardingsphere/issues/39763) · [PR #39764](https://github.com/apache/shardingsphere/pull/39764) | Reproduced a missing terminal callback in a compatibility fallback while auditing hook lifecycles. Fix submitted with unit regression coverage; **unmerged** |
| [ShardingSphere #39765](https://github.com/apache/shardingsphere/issues/39765) | Reproduced and reported Agent span loss during overlapping JDBC executions under controlled conditions; **issue open** |
| [OpenTelemetry instrumentation #20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) | Submitted a fix for JDBC query sanitization settings in OpenTelemetryDriver; **unmerged** |

While building RouteContract, I check assumptions in execution-observation paths and report upstream findings with independent reproductions.
