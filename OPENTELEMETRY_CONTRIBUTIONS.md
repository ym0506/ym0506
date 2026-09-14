# OpenTelemetry contributions

[Profile](./README.md) · [All contributions](./CONTRIBUTIONS.md) · [한국어](./OPENTELEMETRY_CONTRIBUTIONS.ko.md)

## JDBC query sanitization — configuration correctness

**Merged upstream** · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) · September 12, 2026 (UTC)

`OpenTelemetryDriver` ignored JDBC-specific query sanitization settings for `jdbc:otel:` connections. The same setting was already honored by the Java agent and Spring Boot integration, leaving the driver inconsistent.

I fixed the driver to honor JDBC-specific settings from **declarative configuration and system properties**, with the appropriate common defaults and compatibility fallbacks. Deprecated settings now follow the supplied `OpenTelemetry` instance's `v3_preview` value, including when it differs from global configuration.

In the custom-instance preview regression, the driver recorded `SELECT 'test-value'` where sanitization required `SELECT ?`. The fix restores the configured telemetry behavior while preserving the query's returned value. [Before/after verification](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#discussion_r3953704292)

### Scope and verification

- **4 changed files:** 2 production files and 2 test files, covering the driver and shared database configuration helper.
- **16 new H2 regression cases:** defaults, configuration precedence, and per-instance preview behavior. Each checks both the actual query result and the SQL recorded in its span.
- **2 additional `DbConfig` cases:** preview enabled/disabled for deprecated per-instrumentation settings.
- **87 focused test executions passed** in the recorded validation: 16 new H2 cases and 22 existing driver cases in each of the default/stable database semantic-convention modes, plus 11 total `DbConfig` cases. This includes existing tests and repeated execution across modes. [Validation record](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#discussion_r3953704292)

[Merged implementation](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/723bbec482f2b30517862da16204ef900e0eed5f/instrumentation/jdbc/library/src/main/java/io/opentelemetry/instrumentation/jdbc/OpenTelemetryDriver.java#L80-L92) · [H2 tests](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/723bbec482f2b30517862da16204ef900e0eed5f/instrumentation/jdbc/library/src/test/java/io/opentelemetry/instrumentation/jdbc/OpenTelemetryDriverQuerySanitizationTest.java#L55-L179) · [Maintainer approval](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#pullrequestreview-5187716433) · [Required CI passed](https://github.com/open-telemetry/opentelemetry-java-instrumentation/actions/runs/34188337129/job/101954706842)

This contribution addresses the driver query sanitization part of the broader [JDBC stabilization work](https://github.com/open-telemetry/opentelemetry-java-instrumentation/issues/19644). Its engineering focus is configuration precedence, compatibility, and observable behavior verified against a real JDBC database.

<sub>Verified September 14, 2026. Test counts describe the recorded regression validation.</sub>
