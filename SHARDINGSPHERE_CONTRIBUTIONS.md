# Apache ShardingSphere Contributions

[한국어](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) · [Back to profile](./README.md)

As of 2026-08-31: **10 merged pull requests** consisting of 6 production-code fixes or enhancements, 1 CI reliability change, 1 regression-coverage change, and 2 documentation or release-note updates.

## Production Code

| PR | Problem and engineering result | Evidence |
|---|---|---|
| [#38187: Preserve unary `NOT` for PostgreSQL scalar-subquery table extraction](https://github.com/apache/shardingsphere/pull/38187) | Unary `NOT` could fall back to a common expression, hiding nested sharding tables from table extraction and rewrite. Preserved it as `NotExpression`, then narrowed the first broad condition so `IS NOT` remained unaffected. | 2 files · 1,147 PostgreSQL parser ITs reported passing · 148 checks |
| [#38223: Scope MySQL constraint metadata by schema](https://github.com/apache/shardingsphere/pull/38223) | Added `TABLE_SCHEMA = ?` to the `KEY_COLUMN_USAGE` lookup so metadata refresh does not scan unrelated schemas. | 2 files · 148 checks |
| [#38327: Add upgrade-safe HASH_MOD numeric normalization](https://github.com/apache/shardingsphere/pull/38327) | Equal negative numeric values could route differently across `Integer`, `Long`, and `BigInteger`. Added opt-in normalization, preserved legacy routing by default, and covered boundaries and values outside the integer range. | 6 files · 82 checks |
| [#38405: Guard SQL Federation project pushdown against correlation](https://github.com/apache/shardingsphere/pull/38405) | Prevented correlated expressions from being pushed into `LogicalScan` while preserving normal pushdown. | 114 targeted tests · MySQL/PostgreSQL/openGauss E2E · 141 checks |
| [#38449: Make generated sharding index names length-safe and recoverable](https://github.com/apache/shardingsphere/pull/38449) | Introduced legacy-first deterministic naming and candidate-verified logical-name recovery across rewrite and metadata lifecycle paths. | 36 files · +1,383/-74 · 12 commits · 5 change-request rounds · 87 checks |
| [#38659: Preserve empty-input cardinality for window aggregates](https://github.com/apache/shardingsphere/pull/38659) | Restored PostgreSQL/openGauss `OVER` metadata through parser and binder paths so a window aggregate over zero input rows returns zero rows instead of one synthesized `NULL` row. | 10 files · parser/binder/merge regression · 79 checks |

## CI Reliability

| PR | Problem and engineering result | Evidence |
|---|---|---|
| [#38352: Reuse one source snapshot across E2E-SQL jobs](https://github.com/apache/shardingsphere/pull/38352) | Replaced late downstream checkouts of volatile synthetic PR merge refs with a source snapshot created once and reused by matrix jobs. Excluding unused `docs/` reduced the locally measured archive from about 109 MB to 5.1 MB. | 1 workflow file · local archive reduction 95.3% · 89 passed and 2 skipped checks |

The 95.3% figure is an archive-size measurement, not a claim of equivalent workflow-time or failure-rate improvement.

## Regression Coverage

| PR | Problem and engineering result | Evidence |
|---|---|---|
| [#38685: Cover recreation and runtime behavior after dropping an encrypt rule](https://github.com/apache/shardingsphere/pull/38685) | Added focused coverage for named rule-item deletion, same-name recreation, `LOAD SINGLE TABLE`, and post-drop DML rewrite behavior. This was a test-only contribution and did not change production code. | 5 files · 4 maintainer change-request rounds before approval · 64 passed and 2 skipped checks |

## Documentation and Release Completeness

| PR | Result | Evidence |
|---|---|---|
| [#38206: Add missing DistSQL RAL documentation](https://github.com/apache/shardingsphere/pull/38206) | Added English and Chinese syntax pages for `EXPORT METADATA`, `IMPORT METADATA`, and `EXPORT STORAGE NODES`. | 6 pages · 344 added lines |
| [#39535: Add the Cartesian routing fix to 5.5.4 release notes](https://github.com/apache/shardingsphere/pull/39535) | Added the missing release-note entry for an already merged routing correction. | 1 release-note line · 4 checks |

## How to Read the Numbers

- File and commit counts indicate change scope, not user impact.
- Check counts are GitHub check runs, not test-case counts.
- Targeted test counts are tied to the stated verification command, not the full project test suite.
- No user-count, production incident reduction, latency, or throughput improvement is claimed without direct evidence.
