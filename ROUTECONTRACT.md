# RouteContract — Project and Evidence

[Profile](./README.md) · [한국어](./ROUTECONTRACT.ko.md) · [Repository / install](https://github.com/ym0506/routecontract)

**A Java test library that detects changed DB execution patterns behind unchanged business results.**

RouteContract captures physical JDBC execution attempts reported by ShardingSphere-JDBC's `SQLExecutionHook` for a named application operation and compares them with a reviewed manifest. It adds execution budgets, data-source checks, and rewritten-SQL structure checks alongside existing business-result assertions, with Markdown and JSON review reports.

[Run the real MySQL demo on GitHub](https://github.com/ym0506/routecontract/blob/main/docs/first-project.md#try-in-your-browser). The workflow runs the database test in your fork.

## Same result, different execution

| Fixed real-MySQL fixture | Approved query | Changed query |
| --- | --- | --- |
| Returned business row | `(201, 3, PAID)` | Unchanged |
| Observed JDBC execution attempts / data sources | 1 / 1 | 2 / 2 |
| Contract result | `MATCH` | Rejected with `RCM201` · `RCM202` |

Both queries pass the existing result assertion. RouteContract fails CI when the change exceeds the declared attempt or data-source budget. Intentional changes require a person to review the diff and update the approved baseline. The `1→2` result demonstrates regression detection, not a performance improvement. [v0.1.4 public consumer verification](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central.md#public-consumer-verification)

[Recorded manifests](https://github.com/ym0506/routecontract/tree/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/manifests) · [Example CI report](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/docs/evidence/ci-review-report-example.md)

## Same count, wrong destination

In an independent MySQL reproduction of a previously reported ShardingSphere defect, **one affected row and one execution attempt** still meant a write reached `shadow` instead of the expected `primary`. An expected-data-source assertion rejected it; direct reads of both target databases confirmed the destination. Normal `INSERT VALUES` cases provided controls.

The [reproduction and expected CI failure](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/experiments/shadow-insert-select/README.md) use published RouteContract 0.1.3 and ShardingSphere-JDBC 5.5.3. **thswlsqls** authored the [original issue #39750](https://github.com/apache/shardingsphere/issues/39750) and proposed fix; my contribution here is the released-version reproduction and contract demonstration.

## Application code, isolated experiments

Three experiments use selected code from public projects with RouteContract 0.1.3
and synthetic data. Each asks which execution property the test needs to check.

| Project inputs | Observation | Testing decision |
| --- | --- | --- |
| Egon-COLA routing classes and rules | Same row and one attempt; the observed data-source alias changes. Reordering the map and restoring it both match. | Compare source identity when counts cannot express the requirement. |
| SCG mapper, entity and configuration | Two existing queries return the same order with one versus eight observed attempts. | Choose a budget per operation; a difference alone does not establish a regression. |
| CityPulse existing integration test | One patched test passes; its final lookup reports two attempts across one observed alias. | Add capture alongside existing result assertions; no application baseline was approved. |

These are author-run evaluations, not independently completed integrations or production results. The recorded 0.1.3 Java 21/PostgreSQL paths retain their original limits; current Java 17/21 support is separately verified below. [Source revisions, commands and observations](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/application-evaluations.md).

## Verified results

| Check | Result | Conditions and evidence |
| --- | --- | --- |
| v0.1.4 Java/MySQL source tests | **64 passed**, zero failures, errors, or skips | [Immutable release test summary](https://github.com/ym0506/routecontract/releases/download/v0.1.4/test-summary.txt) |
| Structural determinism | **8 cases × 20 repetitions = 160 captures**, one signature per case | Fixed corpus in v0.1.4. [Release source](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/ObservedExecutionRegressionCorpusMySqlTest.java#L414) |
| Operation attribution | **20 pairs** of concurrently open caller scopes, **zero mixed captures** | Single-attempt and fan-out pairs in v0.1.4. [Release source](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/OperationCorrelationMySqlTest.java#L190) |
| Public v0.1.4 consumers | **4 environments: Maven/Gradle × Java 17/21** | MySQL match → expected rejection → restored match, using the public JAR. [Runtime evidence](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central/first-project-java17-java21.json) |

The [v0.1.4 release run](https://github.com/ym0506/routecontract/actions/runs/34681713954) and separate [public-consumer run](https://github.com/ym0506/routecontract/actions/runs/34779973994) passed against ShardingSphere-JDBC 5.5.3 and MySQL 8.4.11.

The 160 captures repeat eight fixed cases; they are not 160 distinct scenarios. The concurrent-scope check did not force or measure temporal overlap between physical callbacks. Consumer runs verify distribution and do not establish external adoption.

## What the library adds

RouteContract packages **operation correlation → data-minimized manifests → reviewed-baseline comparison → CI checks and reports**. Generic JDBC tools can observe the same change around physical DataSources; the library supplies the comparison and review workflow. [Measured datasource-proxy comparison](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/docs/empirical-comparison.md)

**v0.1.4 maintenance:** fixed inconsistent diagnostic snapshots when a failure callback overlaps capture closure. One immutable atomic state now publishes the outcome and exception class together, preserving first-terminal-wins. The new unit test reproduced the old failure before the fix. [Implementation and validation #95](https://github.com/ym0506/routecontract/pull/95)

## From a hook audit to a merged upstream fix

Checking the execution information used by RouteContract led to a ShardingSphere defect: a compatibility result could be returned without notifying hooks of the SQL execution failure. I submitted a regression and a fix that preserves existing return values while reporting failure; [PR #39764 was merged](https://github.com/apache/shardingsphere/pull/39764).

The change is one callback relocation plus regression coverage for two execution paths. [Implementation scope, review, and verification](./SHARDINGSPHERE_CONTRIBUTIONS.md#failure-notification-before-compatibility-fallback).

This was a unit-level investigation of ShardingSphere's heterogeneous compatibility path. RouteContract's released support remains the exact JDBC scope below.

## Release and supported scope

**v0.1.4 is published on Maven Central:** `io.github.ym0506.routecontract:routecontract-shardingsphere-5.5:0.1.4`. [Stable release](https://github.com/ym0506/routecontract/releases/tag/v0.1.4) · [Install](https://github.com/ym0506/routecontract#install-014) · [Public files and consumer verification](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central.md)

- **Supported:** Java 17/21 · exact ShardingSphere-JDBC 5.5.3 · synchronous, non-batch `PreparedStatement`.
- **Observation boundary:** hook-reported physical JDBC execution attempts and structure. These results do not establish a complete route plan, transaction commit, latency, or production throughput.

<sub>Checked: 2026-09-14. v0.1.4 source: a1eb220; public-consumer records: 221014c. Earlier 0.1.3 experiments remain labeled separately.</sub>
