# Open source contributions

[Profile](./README.md) · [한국어](./CONTRIBUTIONS.ko.md)

**12 merged PRs across 3 upstream projects**, verified September 14, 2026.

The work includes **8 runtime code changes**, 1 CI improvement, 1 regression-test contribution, and 2 documentation changes. These categories distinguish changes to product behavior from supporting work.

| Project | Merged | Work and evidence |
| --- | --- | --- |
| Apache ShardingSphere | **10** | SQL correctness, index-name compatibility, opt-in numeric routing, CI, tests, and docs. [Complete PR list and case studies](./SHARDINGSPHERE_CONTRIBUTIONS.md) |
| OpenTelemetry Java instrumentation | **1** | JDBC query-sanitization configuration and per-instance compatibility. [PR #20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) · [Implementation and tests](./OPENTELEMETRY_CONTRIBUTIONS.md) |
| go-mysql-server | **1** | Implemented `NumberType` for **4 system numeric types**, with compile-time interface assertions and numeric/string classification coverage. [PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442) |

## Engineering themes

- **Changes across components:** index-name generation and logical-name recovery across DDL rewriting, metadata refresh, and pipeline paths. [#38449](https://github.com/apache/shardingsphere/pull/38449)
- **Compatibility decisions:** opt-in HASH_MOD normalization preserves legacy routing defaults; JDBC sanitization respects configuration precedence and instance boundaries. [#38327](https://github.com/apache/shardingsphere/pull/38327) · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)
- **Result correctness:** preserve window metadata for empty-input semantics and prevent invalid pushdown of correlated expressions. [#38659](https://github.com/apache/shardingsphere/pull/38659) · [#38405](https://github.com/apache/shardingsphere/pull/38405)

File counts describe implementation scope. Byte lengths, row counts, and type counts describe specific corrected behavior; test counts describe recorded validation. Performance or production adoption is stated only when measured and evidenced.

[RouteContract](./ROUTECONTRACT.md) is my separately authored and published library; its own repository PRs are excluded from the upstream total.
