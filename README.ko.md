# Java · SQL · 관측성

**Apache ShardingSphere**와 **OpenTelemetry**에 기여합니다. 조회 결과가 여전히 맞아도 달라진 DB 실행을 검출하는 Java 라이브러리 **RouteContract**를 개발합니다.

**[외부 프로젝트 3곳에 PR 12건 병합](./CONTRIBUTIONS.ko.md)** · **[RouteContract Maven Central 배포](./ROUTECONTRACT.ko.md)**<br>
[English](./README.md) · [SQL 엔진 기여](#apache-shardingsphere) · [OpenTelemetry](#opentelemetry-java-instrumentation) · [RouteContract](#routecontract)

## Apache ShardingSphere

**병합 PR 10건 · 런타임 개선 6건**과 CI·회귀 테스트·문서 기여가 포함됩니다.

**인덱스 이름 — 호환성 개선** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
생성된 이름이 DB 식별자 길이 제한을 넘는 문제를 해결하고, 논리 이름 복원과 제한 안에 드는 기존 이름 보존까지 처리했습니다. **범위: DDL 재작성·메타데이터 갱신·pipeline 경로와 테스트의 36개 파일.** PostgreSQL 회귀 예제에서 **85 → 63 UTF-8 바이트**로 제한을 충족합니다.

**HASH_MOD — 선택적으로 활성화하는 라우팅 기능 추가** · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
부호 있는 32비트 정수 범위의 같은 값이 **Integer·Long·BigInteger 3종**에서 같은 shard로 가도록 정규화를 추가했습니다. 업그레이드 호환성을 위해 기존 라우팅을 기본값으로 유지했습니다.

**윈도 집계 — SQL 결과 정확성 수정** · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
parser·binder에서 윈도 함수 정보를 보존해 **PostgreSQL·openGauss 2개 방언**의 빈 입력 결과를 **잘못된 NULL 1행 → 올바른 0행**으로 수정했습니다.

[전체 PR 10건·설계 판단·검증 근거](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)

## OpenTelemetry Java instrumentation

**병합 PR 1건 · JDBC 설정 정확성 수정** · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

`OpenTelemetryDriver`가 무시하던 SQL 마스킹 설정을 반영해, span에 기록되는 SQL이 설정된 동작을 따르도록 수정했습니다. JDBC 전용 설정이 공통 기본값보다 우선하도록 반영했습니다. Statement·PreparedStatement의 실제 쿼리 결과와 기록된 SQL을 확인하는 **H2 회귀 사례 16개**를 추가했습니다.

[구현·회귀 검증·메인테이너 승인 근거](./OPENTELEMETRY_CONTRIBUTIONS.ko.md)

## RouteContract

**직접 개발한 Java 라이브러리 · v0.1.4 Maven Central 배포**

**조회 결과는 같지만 DB 실행은 달라질 수 있습니다.** 실제 MySQL 예제에서 업무 결과는 그대로인데 관측 JDBC 실행 시도가 **1 → 2회**로 늘어납니다. RouteContract는 검토된 기준과 비교해 이 변경을 CI에서 거부합니다.

- **구현:** operation별 수집, 실행 예산, 데이터 소스·재작성 SQL 검사, Markdown·JSON 검토 리포트.
- **검증:** **릴리스 소스 테스트 64개 통과**, **Java 17·21 × Gradle·Maven**의 공개 배포물 설치·실행 검증.
- **적용 실험:** **공개 프로젝트 3곳**의 일부 코드를 사용한 작성자 주도의 격리 DB 실험. [결과와 범위](./ROUTECONTRACT.ko.md#애플리케이션-코드를-사용한-격리-실험).

[MySQL 시연 실행](https://github.com/ym0506/routecontract/blob/main/docs/first-project.ko.md#브라우저에서-체험하기) · [설치](https://github.com/ym0506/routecontract#install-014) · [검증 근거](./ROUTECONTRACT.ko.md)

<sub>RouteContract: 정확히 ShardingSphere-JDBC 5.5.3, 동기식·비배치 PreparedStatement. 실행 횟수는 통제된 예제에서 hook으로 관측한 실행 시도입니다. 상태 확인: 2026-09-14.</sub>
