[한국어](./README.ko.md)

## Apache ShardingSphere

**10 merged PRs · 6 runtime improvements**

**Same value, different shard** · New opt-in feature · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
Added HASH_MOD normalization so equal signed 32-bit integer values route consistently across **Integer, Long, and BigInteger**. Kept legacy routing as the default for upgrade compatibility.

**Index names exceeding database limits** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
Fixed name generation and logical-name recovery across DDL rewriting, metadata refresh, and pipeline paths, preserving existing names that fit. **36 files including tests; PostgreSQL regression fixture: 85 → 63 UTF-8 bytes.**

**A NULL row from empty window input** · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
Preserved window metadata through parser and binder paths, correcting empty-input results in **PostgreSQL and openGauss: 1 incorrect NULL row → 0 rows**.

[All 10 PRs and design decisions](./SHARDINGSPHERE_CONTRIBUTIONS.md)

## OpenTelemetry Java instrumentation

**Fixed ignored JDBC SQL masking settings** · [#20020 — merged](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

Updated `OpenTelemetryDriver` to honor JDBC-specific settings, common defaults, and per-instance preview settings. **16 new H2 regression cases** check actual query results and recorded SQL through Statement and PreparedStatement.

[Implementation, tests, and maintainer review](./OPENTELEMETRY_CONTRIBUTIONS.md)

## RouteContract

**Java library author · v0.1.4 on Maven Central**

Built a Java test library that catches changed database execution even when query results stay the same. In a controlled MySQL example, **1 → 2 observed JDBC execution attempts** triggers CI rejection against a reviewed baseline.

Checks execution budgets, data sources, and rewritten SQL; generates Markdown/JSON review reports. The public release was verified across **Java 17/21 × Gradle/Maven**.

[Run the MySQL demo](https://github.com/ym0506/routecontract/blob/main/docs/first-project.md#try-in-your-browser) · [Install](https://github.com/ym0506/routecontract#install-014) · [Tests and isolated experiments](./ROUTECONTRACT.md)

<sub>Scope: ShardingSphere-JDBC 5.5.3; synchronous, non-batch PreparedStatement. Counts are hook-observed attempts.</sub>

---

[All 12 merged upstream PRs across 3 projects](./CONTRIBUTIONS.md) · [Email](mailto:atat9828@naver.com)

<sub>Contribution and release status checked: 2026-09-14.</sub>
