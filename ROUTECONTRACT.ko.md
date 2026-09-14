# RouteContract — 프로젝트와 검증 결과

[한국어 프로필](./README.ko.md) · [English evidence](./ROUTECONTRACT.md) · [저장소·설치](https://github.com/ym0506/routecontract)

**업무 결과가 같아도 달라지는 DB 실행 패턴을 CI에서 검사하는 Java 테스트 라이브러리입니다.**

ShardingSphere-JDBC의 `SQLExecutionHook`이 보고한 물리 JDBC 실행 시도를 operation별로 수집하고, 사람이 승인한 manifest와 비교합니다. 기존 업무 결과 assertion에 실행 예산·데이터 소스·rewritten-SQL 구조 검사를 더하고, 변경 내용을 Markdown·JSON 리포트로 제공합니다.

[GitHub에서 실제 MySQL 시연](https://github.com/ym0506/routecontract/blob/main/docs/first-project.ko.md#브라우저에서-체험하기)을 실행할 수 있습니다. Fork한 저장소의 워크플로에서 데이터베이스 테스트를 실행합니다.

## 같은 결과, 달라진 실행

| 실제 MySQL 고정 사례 | 승인된 조회 | 변경된 조회 |
| --- | --- | --- |
| 반환한 업무 행 | `(201, 3, PAID)` | 동일 |
| 관측된 JDBC 실행 시도 / 데이터 소스 | 1회 / 1개 | 2회 / 2개 |
| 계약 판정 | `MATCH` | `RCM201` · `RCM202`로 실패 |

기존 결과 assertion은 두 조회를 모두 통과시킵니다. RouteContract는 선언한 실행 시도·데이터 소스 예산을 넘는 변경을 CI에서 거부합니다. 의도된 변경이면 담당자가 diff를 검토해 승인본을 갱신합니다. `1→2`는 회귀 검출 사례이며 성능 개선률이 아닙니다. [v0.1.4 공개 소비자 검증](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central.md#public-consumer-verification)

[실제 manifest](https://github.com/ym0506/routecontract/tree/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/manifests) · [CI 리포트 예시](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/docs/evidence/ci-review-report-example.md)

## 횟수는 같지만 잘못된 데이터베이스에 쓰는 경우

이미 보고된 ShardingSphere 결함을 실제 MySQL에서 독립 재현했습니다. **영향받은 행 1개·실행 시도 1회**를 유지해도 예상한 `primary` 대신 `shadow`에 쓰였습니다. 예상 데이터 소스 assertion이 이를 거부했고, 두 대상 DB를 직접 읽어 실제 도착지를 확인했습니다. 정상 `INSERT VALUES` 사례 두 개를 통제 조건으로 함께 실행했습니다.

[재현 코드와 의도한 CI 실패](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/experiments/shadow-insert-select/README.md)는 공개 RouteContract 0.1.3·ShardingSphere-JDBC 5.5.3을 사용합니다. **원 이슈와 수정 제안은 thswlsqls의 작업**입니다. 제 기여는 출시판 재현과 실행 계약을 통한 검출 증거입니다. [원 이슈 #39750](https://github.com/apache/shardingsphere/issues/39750)

## 애플리케이션 코드를 사용한 격리 실험

공개 프로젝트 세 곳의 일부 코드와 합성 데이터를 RouteContract 0.1.3으로 실행했습니다.
각 실험에서 어떤 실행 속성을 검사해야 하는지 확인했습니다.

| 사용한 프로젝트 코드 | 관측 결과 | 테스트에서 판단할 점 |
| --- | --- | --- |
| Egon-COLA 라우팅 클래스와 규칙 | 같은 행·실행 시도 1회를 유지하면서 관측 데이터 소스가 바뀜. 설정 항목 순서 변경과 원복은 일치. | 횟수만으로 요구를 표현할 수 없으면 데이터 소스 식별자를 비교합니다. |
| SCG mapper·엔티티·설정 | 기존 조회 두 개가 같은 주문을 반환하면서 실행 시도는 1회와 8회. | operation별 예산을 정합니다. 차이가 있다는 이유만으로 회귀라고 판단하지 않습니다. |
| CityPulse 기존 통합 테스트 | 수정한 테스트 한 개 통과. 마지막 조회에서 실행 시도 2회·관측 데이터 소스 1개. | 기존 결과 assertion에 capture를 추가했습니다. 애플리케이션 기준값을 승인한 것은 아닙니다. |

모두 작성자 측의 자체 평가이며 독립적인 도입·운영 실적이 아닙니다. 0.1.3 당시 Java 21·PostgreSQL 관측의 범위는 그대로 유지하고, 현재 Java 17/21 지원은 아래에서 별도로 검증했습니다. [고정된 소스·재현 명령·관측 결과](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/application-evaluations.ko.md)

## 검증 수치

| 검증 항목 | 결과 | 조건·근거 |
| --- | --- | --- |
| v0.1.4 Java/MySQL 소스 테스트 | **64개 통과**, 실패·오류·skip 각 0 | [불변 릴리스 테스트 요약](https://github.com/ym0506/routecontract/releases/download/v0.1.4/test-summary.txt) |
| 구조적 결정성 | **8개 사례 × 20회 = 160 captures**, 사례별 signature 1개 | v0.1.4의 고정 corpus 반복. [릴리스 소스](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/ObservedExecutionRegressionCorpusMySqlTest.java#L414) |
| operation 기록의 귀속 | 동시에 열린 caller scope **20쌍**, 교차 귀속 **0건** | v0.1.4의 단일 실행·fan-out 쌍. [릴리스 소스](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/OperationCorrelationMySqlTest.java#L190) |
| 공개 v0.1.4 소비자 실행 | **Maven/Gradle × Java 17/21, 4개 환경** | 공개 JAR·MySQL로 일치 → 의도한 거부 → 원복 후 일치 확인. [실제 실행 기록](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central/first-project-java17-java21.json) |

[v0.1.4 릴리스 실행](https://github.com/ym0506/routecontract/actions/runs/34681713954)과 별도의 [공개 소비자 실행](https://github.com/ym0506/routecontract/actions/runs/34779973994)이 ShardingSphere-JDBC 5.5.3·MySQL 8.4.11 환경에서 통과했습니다.

160회는 서로 다른 시나리오 160개가 아닌 고정 사례의 반복 횟수입니다. 동시 scope 검증은 물리 callback의 시간상 중첩을 강제하거나 측정하지 않았습니다. 소비자 실행은 배포 검증이며 외부 사용자 채택을 뜻하지 않습니다.

## 구현의 차별점

RouteContract는 **operation 연계 → 최소정보 manifest → 승인본 비교 → CI 판정·리포트**를 하나의 라이브러리로 제공합니다. 범용 JDBC 도구도 각 물리 DataSource에서 같은 변화를 관측할 수 있으며, 이 라이브러리는 비교와 검토 절차를 함께 제공합니다. [datasource-proxy와의 실제 비교](https://github.com/ym0506/routecontract/blob/a1eb22087eaf3a49e894d12ba56516efac99343f/docs/empirical-comparison.md)

**v0.1.4 유지보수:** 실패 콜백과 캡처 종료가 겹칠 때 진단 스냅샷의 상태가 불일치하는 결함을 수정했습니다. 결과와 예외 클래스를 하나의 불변 상태로 원자적으로 게시하고, 먼저 보고된 종료 상태를 유지합니다. 새 단위 테스트로 수정 전 실패를 재현했습니다. [구현·검증 #95](https://github.com/ym0506/routecontract/pull/95)

## 배포와 지원 범위

**v0.1.4를 Maven Central에서 사용할 수 있습니다.** 좌표는 `io.github.ym0506.routecontract:routecontract-shardingsphere-5.5:0.1.4`입니다. [정식 릴리스](https://github.com/ym0506/routecontract/releases/tag/v0.1.4) · [설치](https://github.com/ym0506/routecontract#install-014) · [공개 파일·소비자 검증](https://github.com/ym0506/routecontract/blob/221014cb01eaa4a1be67e2a941ea665ef24f0a4b/docs/evidence/release-0.1.4-central.md)

- **지원:** Java 17/21 · 정확히 ShardingSphere-JDBC 5.5.3 · 동기식·비배치 `PreparedStatement`.
- **관측 범위:** hook이 보고한 물리 JDBC 실행 시도와 구조. 전체 라우팅 계획·트랜잭션 commit·응답 시간·운영 처리량을 측정한 결과는 아닙니다.
- **개발 중:** core/adapter 분리를 다루는 [0.2 PR #62](https://github.com/ym0506/routecontract/pull/62)는 미출시 작업이며 현재 지원 범위에 포함하지 않습니다.

<sub>확인: 2026-09-14. v0.1.4 소스는 a1eb220, 공개 소비자 기록은 221014c에 고정했습니다. 이전 0.1.3 실험은 별도로 표시했습니다.</sub>
