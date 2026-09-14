[한국어](./README.ko.md)

## Apache ShardingSphere

**7 SQL execution and compatibility improvements · 11 merged PRs**

**Index-name compatibility across DDL, metadata, and pipeline** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
Fixed generated names exceeding database limits and restored logical-name recovery across SQL rewriting, metadata refresh, and pipeline paths. Preserved existing names that fit. **Scope: 36 files, including tests.**

**Same value, different shard** · Opt-in routing feature · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
Added HASH_MOD normalization so equal signed 32-bit integer values route consistently across **Integer, Long, and BigInteger**. Kept legacy routing as the default for upgrade compatibility.

**Missing failure notification before compatibility fallback** · Reliability fix · [Issue #39763](https://github.com/apache/shardingsphere/issues/39763) → [Merged PR #39764](https://github.com/apache/shardingsphere/pull/39764)<br>
While auditing execution hooks for RouteContract, fixed a missing SQL failure notification before a compatibility fallback. Regression tests verify preserved return values and exactly one failure notification in **trunk and worker paths**.

[All 11 PRs and design decisions](./SHARDINGSPHERE_CONTRIBUTIONS.md)

## OpenTelemetry Java instrumentation

**JDBC SQL masking settings now respected** · [#20020 — merged](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

Made SQL recorded in JDBC spans follow the configured masking settings while preserving query results. Updated `OpenTelemetryDriver` and shared DB configuration; **16 new H2 regression cases** cover Statement and PreparedStatement.

[Implementation, tests, and maintainer review](./OPENTELEMETRY_CONTRIBUTIONS.md)

## RouteContract

**Java library author · v0.1.4 on Maven Central**

**Detects execution changes that result assertions miss.** In a controlled MySQL example, the query result stays unchanged while observed JDBC execution attempts increase **1 → 2**. RouteContract rejects the change against a reviewed baseline in CI.

Checks execution budgets, data sources, and rewritten SQL; generates Markdown/JSON review reports. The public release was verified across **Java 17/21 × Gradle/Maven**.

[Run the MySQL demo](https://github.com/ym0506/routecontract/blob/main/docs/first-project.md#try-in-your-browser) · [Install](https://github.com/ym0506/routecontract#install-014) · [Tests and isolated experiments](./ROUTECONTRACT.md)

<sub>Scope: ShardingSphere-JDBC 5.5.3; synchronous, non-batch PreparedStatement. Counts are hook-observed attempts.</sub>

---

[All 13 merged upstream PRs across 3 projects](./CONTRIBUTIONS.md) · [Email](mailto:atat9828@naver.com)

<sub>Contribution and release status checked: 2026-09-14.</sub>
