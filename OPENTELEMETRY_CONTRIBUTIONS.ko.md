# OpenTelemetry 기여

[프로필](./README.ko.md) · [전체 기여](./CONTRIBUTIONS.ko.md) · [English](./OPENTELEMETRY_CONTRIBUTIONS.md)

## JDBC query sanitization — 설정 정확성 수정

**Upstream 병합 완료** · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) · 2026년 9월 13일 (KST)

`OpenTelemetryDriver`는 `jdbc:otel:` 연결에서 JDBC 전용 query sanitization 설정을 무시했습니다. Java agent와 Spring Boot 통합에서는 이미 적용되던 설정이 driver 경로에서는 다르게 동작했습니다.

**Declarative configuration과 system property**에서 JDBC 전용 설정을 반영하도록 수정하고, 공통 기본값과 기존 설정의 fallback 규칙을 유지했습니다. Deprecated 설정을 적용할 때에도 전역 설정 대신 전달받은 `OpenTelemetry` 인스턴스의 `v3_preview` 값을 따르도록 고쳤습니다.

사용자 지정 인스턴스의 preview 회귀 사례에서는 sanitization 설정상 `SELECT ?`를 기록해야 하는데 `SELECT 'test-value'`가 기록됐습니다. 수정 후 실제 쿼리 반환 값은 유지하면서 span에 기록되는 SQL이 설정을 따르게 했습니다. [수정 전후 검증](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#discussion_r3953704292)

### 변경 범위와 검증

- **변경 파일 4개:** 운영 코드 2개와 테스트 2개. Driver와 공통 DB 설정 helper를 수정했습니다.
- **H2 회귀 사례 16개 추가:** 기본값, 설정 우선순위, 인스턴스별 preview 동작을 검증합니다. 각 사례에서 실제 쿼리 결과와 span에 기록된 SQL을 함께 확인합니다.
- **`DbConfig` 사례 2개 추가:** Deprecated JDBC 설정이 preview 활성화·비활성화에 따라 적용되는지 확인합니다.
- **관련 테스트 실행 87건 통과:** Default/stable DB semantic conventions에서 각각 신규 H2 사례 16개와 기존 driver 사례 22개를 실행하고, `DbConfig` 전체 11개를 검증한 기록입니다. 기존 테스트와 모드별 반복 실행을 포함한 수치입니다. [검증 기록](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#discussion_r3953704292)

[병합된 구현](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/723bbec482f2b30517862da16204ef900e0eed5f/instrumentation/jdbc/library/src/main/java/io/opentelemetry/instrumentation/jdbc/OpenTelemetryDriver.java#L80-L92) · [H2 테스트](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/723bbec482f2b30517862da16204ef900e0eed5f/instrumentation/jdbc/library/src/test/java/io/opentelemetry/instrumentation/jdbc/OpenTelemetryDriverQuerySanitizationTest.java#L55-L179) · [Maintainer 승인](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020#pullrequestreview-5187716433) · [필수 CI 통과](https://github.com/open-telemetry/opentelemetry-java-instrumentation/actions/runs/34188337129/job/101954706842)

이 PR은 [JDBC 안정화 작업](https://github.com/open-telemetry/opentelemetry-java-instrumentation/issues/19644) 중 driver query sanitization 부분을 해결했습니다. 설정 우선순위와 하위 호환성을 다루고, 실제 JDBC 데이터베이스를 이용해 관측되는 동작까지 검증한 기여입니다.

<sub>2026년 9월 14일 확인. 테스트 수치는 당시 회귀 검증 기록을 기준으로 합니다.</sub>
