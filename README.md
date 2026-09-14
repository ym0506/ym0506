# Java · SQL correctness · Observability

Contributor to **Apache ShardingSphere** and **OpenTelemetry**. Author of **RouteContract**, a Java library that catches database execution regressions even when query results stay unchanged.

**[12 merged upstream PRs across 3 projects](./CONTRIBUTIONS.md)** · **[RouteContract on Maven Central](./ROUTECONTRACT.md)**<br>
[한국어](./README.ko.md) · [SQL engine work](#apache-shardingsphere) · [OpenTelemetry](#opentelemetry-java-instrumentation) · [RouteContract](#routecontract)

## Apache ShardingSphere

**10 merged PRs · 6 runtime improvements**, plus CI, regression tests, and documentation.

**Index naming — compatibility enhancement** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
Fixed generated names that exceeded database identifier limits, including logical-name recovery and preservation of existing names when safe. **Scope: 36 files** across DDL rewriting, metadata refresh, pipeline paths, and tests. A PostgreSQL regression fixture now meets the limit: **85 → 63 UTF-8 bytes**.

**HASH_MOD — new opt-in routing feature** · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
Made equal signed 32-bit integer values route consistently across **3 types: Integer, Long, and BigInteger**. Kept legacy routing as the default to preserve upgrade compatibility.

**Window aggregates — SQL correctness fix** · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
Restored correct empty-input results in **2 dialects, PostgreSQL and openGauss: 1 incorrect NULL row → 0 rows**, by preserving window metadata through parser and binder paths.

[All 10 PRs, design decisions, and evidence](./SHARDINGSPHERE_CONTRIBUTIONS.md)

## OpenTelemetry Java instrumentation

**1 merged PR · JDBC configuration correctness** · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

Fixed ignored query-sanitization settings in `OpenTelemetryDriver`, so SQL recorded in spans follows the configured masking behavior. JDBC-specific settings now override common defaults. Added **16 H2 regression cases** that check real query results and recorded SQL through Statement and PreparedStatement.

[Implementation, regression coverage, and maintainer approval](./OPENTELEMETRY_CONTRIBUTIONS.md)

## RouteContract

**Java library author · v0.1.4 on Maven Central**

**Same query result, different database execution.** In a real-MySQL fixture, observed JDBC execution attempts increase **1 → 2** while the business result stays unchanged. RouteContract rejects the change against a reviewed baseline in CI.

- **Built:** operation capture, execution budgets, data-source and rewritten-SQL checks, and Markdown/JSON review reports.
- **Verified:** **64 release source tests passed**; public-artifact consumer checks cover **Java 17/21 × Gradle/Maven**.
- **Explored:** selected code from **3 public projects** in isolated, author-run database experiments. [Results and scope](./ROUTECONTRACT.md#application-code-isolated-experiments).

[Run the MySQL demo](https://github.com/ym0506/routecontract/blob/main/docs/first-project.md#try-in-your-browser) · [Install](https://github.com/ym0506/routecontract#install-014) · [Evidence](./ROUTECONTRACT.md)

<sub>RouteContract: exact ShardingSphere-JDBC 5.5.3; synchronous, non-batch PreparedStatement. Attempt counts describe hook observations in controlled fixtures. Status checked: 2026-09-14.</sub>
