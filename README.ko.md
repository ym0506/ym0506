[English](./README.md)

## Apache ShardingSphere

**SQL 실행·호환성 개선 7건 · 전체 병합 PR 11건**

**DDL·메타데이터·파이프라인에 걸친 인덱스 이름 수정** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
DB 길이 제한을 넘는 이름을 생성하는 문제와 논리 이름 복원을 함께 처리했습니다. SQL 재작성·메타데이터 갱신·파이프라인 경로를 수정하고, 제한 안에 드는 기존 이름은 유지했습니다. **구현 범위: 테스트를 포함한 36개 파일.**

**같은 값이 다른 샤드로 가는 문제** · 선택형 라우팅 기능 추가 · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
부호 있는 32비트 정수 범위의 같은 값이 **Integer·Long·BigInteger**에서 같은 샤드로 가도록 HASH_MOD 정규화를 추가했습니다. 업그레이드 호환성을 위해 기존 라우팅을 기본값으로 유지했습니다.

**호환 처리에서 누락되던 SQL 실패 알림** · 신뢰성 개선 · [이슈 #39763](https://github.com/apache/shardingsphere/issues/39763) → [병합 PR #39764](https://github.com/apache/shardingsphere/pull/39764)<br>
RouteContract를 개발하며 실행 훅을 점검하다, 호환용 결과를 반환할 때 SQL 실패 알림이 누락되는 경로를 찾아 고쳤습니다. 회귀 테스트로 **두 실행 경로**에서 기존 반환값을 유지하고 실패 알림을 한 번만 전달하는지 확인했습니다.

[전체 PR 11건과 설계 판단](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)

## OpenTelemetry Java instrumentation

**JDBC SQL 마스킹 설정 오류 수정** · [#20020 — 병합 완료](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)

`OpenTelemetryDriver`와 공통 DB 설정 처리를 고쳐 스팬에 남는 SQL이 마스킹 설정을 따르도록 했습니다. `Statement`·`PreparedStatement`의 **H2 회귀 사례 16개**를 추가해 기록된 SQL과 실제 쿼리 결과를 함께 검증했습니다.

[구현·테스트·메인테이너 검토](./OPENTELEMETRY_CONTRIBUTIONS.ko.md)

## RouteContract

**직접 개발한 Java 라이브러리 · v0.1.4 Maven Central 배포**

**조회 결과만 확인하는 테스트가 놓치는 DB 실행 변화를 검출합니다.** 실제 MySQL을 사용한 통제된 예제에서 조회 결과는 그대로인데 관측된 JDBC 실행 시도가 **1 → 2회**로 늘어납니다. RouteContract는 검토된 기준과 비교해 이 변경을 CI에서 거부합니다.

실행 횟수·데이터 소스·재작성된 SQL을 검사하고 Markdown·JSON 리포트를 만듭니다. 공개 배포물은 **Java 17·21 × Gradle·Maven**에서 설치와 실행을 검증했습니다.

[MySQL 시연 실행](https://github.com/ym0506/routecontract/blob/main/docs/first-project.ko.md#브라우저에서-체험하기) · [설치](https://github.com/ym0506/routecontract#install-014) · [테스트·격리 실험 결과](./ROUTECONTRACT.ko.md)

<sub>지원 범위: ShardingSphere-JDBC 5.5.3, 동기식·비배치 PreparedStatement. 실행 횟수는 훅으로 관측한 실행 시도입니다.</sub>

---

[외부 프로젝트 3곳에 병합된 PR 13건 전체 보기](./CONTRIBUTIONS.ko.md) · [이메일](mailto:atat9828@naver.com)

<sub>기여·배포 상태 확인: 2026-09-14.</sub>
