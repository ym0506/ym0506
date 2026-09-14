# Java · SQL · 관측성

SQL 엔진과 JDBC 계측의 오류를 재현하고 고칩니다. **Apache ShardingSphere**와 **OpenTelemetry**에 기여하며, 조회 결과만으로는 놓치는 실행 변화를 검사하는 Java 라이브러리 **RouteContract**를 개발해 배포했습니다.

**[외부 프로젝트 3곳에 PR 12건 병합](./CONTRIBUTIONS.ko.md)** · **[RouteContract Maven Central 배포](./ROUTECONTRACT.ko.md)**<br>
[English](./README.md) · [SQL 엔진 기여](#apache-shardingsphere) · [OpenTelemetry](#opentelemetry-java-instrumentation) · [RouteContract](#routecontract)

Java 백엔드·플랫폼 개발 분야의 채용 제안은 [이메일로 보내 주세요](mailto:atat9828@naver.com).

## Apache ShardingSphere

**병합 PR 10건** 중 **런타임 개선이 6건**이며, CI·회귀 테스트·문서에도 기여했습니다.

**길이 제한을 넘는 인덱스 이름** · 호환성 개선 · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
생성된 이름이 DB 식별자 길이 제한을 넘는 문제를 해결하고, 논리 이름 복원과 제한 안에 드는 기존 이름 보존까지 처리했습니다. **범위: DDL 재작성·메타데이터 갱신·파이프라인 경로와 테스트의 36개 파일.** PostgreSQL 회귀 예제에서 **85 → 63 UTF-8 바이트**로 제한을 충족합니다.

**같은 값이 다른 샤드로 가는 문제** · 선택형 HASH_MOD 기능 추가 · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
부호 있는 32비트 정수 범위의 같은 값이 **Integer·Long·BigInteger 3종**에서 같은 샤드로 가도록 정규화를 추가했습니다. 업그레이드 호환성을 위해 기존 라우팅을 기본값으로 유지했습니다.

**빈 입력에서 생기는 NULL 행** · SQL 결과 수정 · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
파서·바인더에서 윈도 함수 정보를 보존해 **PostgreSQL·openGauss 2개 방언**의 빈 입력 결과를 **잘못된 NULL 1행 → 올바른 0행**으로 수정했습니다.

[전체 PR 10건·설계 판단·검증 근거](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)

## OpenTelemetry Java instrumentation

**무시되던 JDBC SQL 마스킹 설정 수정** · 병합 PR 1건 · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

스팬에 남는 SQL이 설정대로 마스킹되도록 `OpenTelemetryDriver`를 수정했습니다. JDBC 전용 설정과 공통 기본값, 인스턴스별 `v3_preview` 설정을 반영했습니다. `Statement`·`PreparedStatement`의 실제 쿼리 결과와 기록된 SQL을 확인하는 **H2 회귀 사례 16개**를 추가했습니다.

[구현·회귀 검증·메인테이너 승인 근거](./OPENTELEMETRY_CONTRIBUTIONS.ko.md)

## RouteContract

**직접 개발한 Java 라이브러리 · v0.1.4 Maven Central 배포**

**조회 결과는 같지만 DB 실행은 달라질 수 있습니다.** 실제 MySQL 예제에서 업무 결과는 그대로인데 관측된 JDBC 실행 시도가 **1 → 2회**로 늘어납니다. RouteContract는 검토된 기준과 비교해 이 변경을 CI에서 거부합니다.

작업별로 실행 기록을 모아 허용한 실행 횟수와 데이터 소스, 재작성된 SQL을 검사하고 Markdown·JSON 리포트를 만듭니다. **릴리스 소스 테스트 64개**가 통과했으며, **Java 17·21 × Gradle·Maven**에서 공개 배포물의 설치와 실행을 확인했습니다.

**공개 프로젝트 3곳**의 일부 코드도 별도 DB 환경에서 직접 실행해 검증했습니다. [실험 결과와 범위](./ROUTECONTRACT.ko.md#애플리케이션-코드를-사용한-격리-실험).

[MySQL 시연 실행](https://github.com/ym0506/routecontract/blob/main/docs/first-project.ko.md#브라우저에서-체험하기) · [설치](https://github.com/ym0506/routecontract#install-014) · [검증 근거](./ROUTECONTRACT.ko.md)

<sub>RouteContract: 정확히 ShardingSphere-JDBC 5.5.3, 동기식·비배치 PreparedStatement. 실행 횟수는 통제된 예제에서 훅으로 관측한 실행 시도입니다. 상태 확인: 2026-09-14.</sub>
