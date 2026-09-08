# RouteContract — Project and Evidence

[Profile](./README.md) · [한국어](./ROUTECONTRACT.ko.md) · [Repository / install](https://github.com/ym0506/routecontract)

**A Java test library that detects changed DB execution patterns behind unchanged business results.**

RouteContract captures physical JDBC execution attempts reported by ShardingSphere-JDBC's `SQLExecutionHook` for a named application operation and compares them with a reviewed manifest. It adds execution budgets, data-source checks, and rewritten-SQL structure checks alongside existing business-result assertions, with Markdown and JSON review reports.

## Same result, different execution

| Fixed real-MySQL fixture | Approved query | Changed query |
| --- | --- | --- |
| Returned business row | `(201, 3, PAID)` | Unchanged |
| Observed physical JDBC execution attempts | 1 | 2 |
| Contract result | `MATCH` | Rejected with `RCM201` · `RCM202` |

Both queries pass the existing result assertion. RouteContract fails CI when the change exceeds the declared attempt or data-source budget. Intentional changes require a person to review the diff and update the approved baseline. The `1→2` result demonstrates regression detection, not a performance improvement. [Public consumer verification](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central.md#public-consumer-verification)

[2m 54s demo](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [Recorded manifests](https://github.com/ym0506/routecontract/tree/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/manifests) · [Example CI report](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/docs/evidence/ci-review-report-example.md)

## Verified results

| Check | Result | Conditions and evidence |
| --- | --- | --- |
| v0.1.3 Java/MySQL source tests | **62 passed**, zero failures, errors, or skips | [Immutable release test summary](https://github.com/ym0506/routecontract/releases/download/v0.1.3/test-summary.txt) |
| Structural determinism | **8 cases × 20 repetitions = 160 captures**, one signature per case | Repeated fixed corpus. [Release source](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/ObservedExecutionRegressionCorpusMySqlTest.java#L414) |
| Operation attribution | **20 pairs** of concurrently open caller scopes, **zero mixed captures** | Single-attempt and fan-out pairs. [Release source](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/OperationCorrelationMySqlTest.java#L190) |
| Consumers installed from Central | **2 build paths: Gradle and Maven**, each passing **3 MySQL tests** | Maintainer-run checks with fresh resolver caches. [Gradle](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central/gradle-summary.json) · [Maven](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central/maven-summary.json) |

The [v0.1.3 release run](https://github.com/ym0506/routecontract/actions/runs/34122514785/job/101743588716) records the corpus repetitions, operation attribution, and `1→2` contract rejection. The release used Java 17, ShardingSphere-JDBC 5.5.3, and MySQL 8.4.11.

The 160 captures repeat eight fixed cases; they are not 160 distinct scenarios. The concurrent-scope check did not force or measure temporal overlap between physical callbacks. Consumer runs verify distribution and do not establish external adoption.

## What the library adds

Generic JDBC tools can observe the same `1→2` change when placed around each physical DataSource. RouteContract packages **operation correlation → data-minimized manifests → reviewed-baseline comparison → CI checks and reports** for its supported ShardingSphere version. The aim is to reduce the custom wiring each project would otherwise build. [Empirical comparison with datasource-proxy](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/docs/empirical-comparison.md)

## Release and supported scope

**v0.1.3 is available on GitHub Releases and Maven Central** as `io.github.ym0506.routecontract:routecontract-shardingsphere-5.5:0.1.3`. [Stable release](https://github.com/ym0506/routecontract/releases/tag/v0.1.3) · [Central POM](https://repo.maven.apache.org/maven2/io/github/ym0506/routecontract/routecontract-shardingsphere-5.5/0.1.3/routecontract-shardingsphere-5.5-0.1.3.pom) · [Central JAR](https://repo.maven.apache.org/maven2/io/github/ym0506/routecontract/routecontract-shardingsphere-5.5/0.1.3/routecontract-shardingsphere-5.5-0.1.3.jar)

The immutable September 7 release notes still say Central publication is pending. The subsequent September 8 [public-file and consumer verification record](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central.md) documents publication and successful installation.

- **Supported:** Java 17 · exact ShardingSphere-JDBC 5.5.3 · synchronous, non-batch `PreparedStatement`.
- **Observation boundary:** hook-reported physical JDBC execution attempts and structure. These results do not establish a complete route plan, transaction commit, latency, or production throughput.
- **In development:** the core/adapter split in [0.2 PR #62](https://github.com/ym0506/routecontract/pull/62) is unreleased and does not extend current support.

<sub>Status checked: 2026-09-08. Source and repeated-test evidence are pinned to v0.1.3 commit f1efd71; the Central publication record is pinned to commit 961c4ba.</sub>
