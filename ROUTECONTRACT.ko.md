# RouteContract — 프로젝트와 검증 결과

[한국어 프로필](./README.ko.md) · [English evidence](./ROUTECONTRACT.md) · [저장소·설치](https://github.com/ym0506/routecontract)

**업무 결과가 같아도 달라지는 DB 실행 패턴을 CI에서 검사하는 Java 테스트 라이브러리입니다.**

ShardingSphere-JDBC의 `SQLExecutionHook`이 보고한 물리 JDBC 실행 시도를 operation별로 수집하고, 사람이 승인한 manifest와 비교합니다. 기존 업무 결과 assertion에 실행 예산·데이터 소스·rewritten-SQL 구조 검사를 더하고, 변경 내용을 Markdown·JSON 리포트로 제공합니다.

[20초 인터랙티브 설명 · English](https://routecontract.ym56.chatgpt.site)에서 예제를 살펴보고,
[GitHub에서 실제 MySQL 시연](https://github.com/ym0506/routecontract/blob/main/docs/first-project.ko.md#브라우저에서-체험하기)을 실행할 수 있습니다.
설명 페이지는 기록된 예제를 보여 주며, 데이터베이스 테스트는 GitHub 워크플로에서 실행합니다.

> RouteContract checks hook-reported physical JDBC execution attempts against reviewed contracts in CI. Its real-MySQL fixture retains the business result while attempts increase from 1 to 2. These figures demonstrate regression detection, not a performance improvement.

## 같은 결과, 달라진 실행

| 실제 MySQL 고정 사례 | 승인된 조회 | 변경된 조회 |
| --- | --- | --- |
| 반환한 업무 행 | `(201, 3, PAID)` | 동일 |
| 관측된 물리 JDBC 실행 시도 | 1회 | 2회 |
| 계약 판정 | `MATCH` | `RCM201` · `RCM202`로 실패 |

기존 결과 assertion은 두 조회를 모두 통과시킵니다. RouteContract는 선언한 실행 시도·데이터 소스 예산을 넘는 변경을 CI에서 거부합니다. 실행 증가가 의도된 변경이면 담당자가 diff를 검토해 승인본을 갱신합니다. [공개 소비자 검증](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central.md#public-consumer-verification)

[실제 manifest](https://github.com/ym0506/routecontract/tree/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/manifests) · [CI 리포트 예시](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/docs/evidence/ci-review-report-example.md)

## 애플리케이션 코드를 사용한 격리 실험

공개 프로젝트 세 곳의 일부 코드와 합성 데이터를 RouteContract 0.1.3으로 실행했습니다.
각 실험에서 어떤 실행 속성을 검사해야 하는지 확인했습니다.

| 사용한 프로젝트 코드 | 관측 결과 | 테스트에서 판단할 점 |
| --- | --- | --- |
| Egon-COLA 라우팅 클래스와 규칙 | 같은 행·실행 시도 1회를 유지하면서 관측 데이터 소스가 바뀜. 설정 항목 순서 변경과 원복은 일치. | 횟수만으로 요구를 표현할 수 없으면 데이터 소스 식별자를 비교합니다. |
| SCG mapper·엔티티·설정 | 기존 조회 두 개가 같은 주문을 반환하면서 실행 시도는 1회와 8회. | operation별 예산을 정합니다. 차이가 있다는 이유만으로 회귀라고 판단하지 않습니다. |
| CityPulse 기존 통합 테스트 | 수정한 테스트 한 개 통과. 마지막 조회에서 실행 시도 2회·관측 데이터 소스 1개. | 기존 결과 assertion에 capture를 추가했습니다. 애플리케이션 기준값을 승인한 것은 아닙니다. |

모두 작성자 측의 자체 평가이며 독립적인 도입·운영 실적이 아닙니다. Java 21·PostgreSQL
관측은 기록된 실행 경로와 의존성 조합에 한정됩니다.
[고정된 소스·재현 명령·원시 관측·한계](https://github.com/ym0506/routecontract/blob/657f14817f59aa76cb9e2c546f2acb72dc780305/docs/application-evaluations.ko.md)를 함께 공개했습니다.

## 검증 수치

| 검증 항목 | 결과 | 조건·근거 |
| --- | --- | --- |
| v0.1.3 Java/MySQL 소스 테스트 | **62개 통과**, 실패·오류·skip 각 0 | [불변 릴리스 테스트 요약](https://github.com/ym0506/routecontract/releases/download/v0.1.3/test-summary.txt) |
| 구조적 결정성 | **8개 사례 × 20회 = 160 captures**, 사례별 signature 1개 | 고정 corpus 반복. [릴리스 소스](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/ObservedExecutionRegressionCorpusMySqlTest.java#L414) |
| operation 기록의 귀속 | 동시에 열린 caller scope **20쌍**, 교차 귀속 **0건** | 단일 실행·fan-out 쌍. [릴리스 소스](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/examples/mysql/src/test/java/io/github/ym0506/routecontract/example/OperationCorrelationMySqlTest.java#L190) |
| Central 설치 후 소비자 실행 | **Gradle·Maven 2개 빌드 경로**, 각각 MySQL 테스트 **3개 통과** | 개발자가 새 resolver cache로 실행. [Gradle](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central/gradle-summary.json) · [Maven](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central/maven-summary.json) |

[v0.1.3 릴리스 실행 로그](https://github.com/ym0506/routecontract/actions/runs/34122514785/job/101743588716)에서도 corpus 반복·operation 귀속·`1→2` 계약 실패 결과를 확인할 수 있습니다. 릴리스는 Java 17·ShardingSphere-JDBC 5.5.3·MySQL 8.4.11 환경을 사용했습니다.

160회는 서로 다른 시나리오 160개가 아닌 고정 사례의 반복 횟수입니다. 동시 scope 검증은 물리 callback의 시간상 중첩을 강제하거나 측정하지 않았습니다. 소비자 실행은 배포 검증이며 외부 사용자 채택을 뜻하지 않습니다.

## 구현의 차별점

범용 JDBC 도구도 각 물리 DataSource를 감싸면 같은 `1→2` 변화를 관측할 수 있습니다. RouteContract는 지원하는 ShardingSphere 버전에 대해 **operation 연계 → 최소정보 manifest → 승인본 비교 → CI 판정·리포트**를 하나의 라이브러리로 제공합니다. 이 흐름을 각 프로젝트에서 따로 조립해야 하는 부담을 줄이는 것이 목적입니다. [datasource-proxy와의 실제 비교](https://github.com/ym0506/routecontract/blob/f1efd71e32078dd5812268a1ad24ee73110ff61f/docs/empirical-comparison.md)

## 배포와 지원 범위

**v0.1.3을 GitHub Release와 Maven Central에서 사용할 수 있습니다.** 좌표는 `io.github.ym0506.routecontract:routecontract-shardingsphere-5.5:0.1.3`입니다. [정식 릴리스](https://github.com/ym0506/routecontract/releases/tag/v0.1.3) · [Central POM](https://repo.maven.apache.org/maven2/io/github/ym0506/routecontract/routecontract-shardingsphere-5.5/0.1.3/routecontract-shardingsphere-5.5-0.1.3.pom) · [Central JAR](https://repo.maven.apache.org/maven2/io/github/ym0506/routecontract/routecontract-shardingsphere-5.5/0.1.3/routecontract-shardingsphere-5.5-0.1.3.jar)

9월 7일의 불변 릴리스 본문에는 Central 게시가 pending으로 남아 있습니다. 이후 9월 8일의 [공개 파일·소비자 검증 기록](https://github.com/ym0506/routecontract/blob/961c4baab5c6b690fdad997ed70806c82e2ede8b/docs/evidence/release-0.1.3-central.md)이 게시 완료와 설치 결과를 보존합니다.

- **지원:** Java 17 · 정확히 ShardingSphere-JDBC 5.5.3 · 동기식·비배치 `PreparedStatement`.
- **관측 범위:** hook이 보고한 물리 JDBC 실행 시도와 구조. 전체 라우팅 계획·트랜잭션 commit·응답 시간·운영 처리량을 측정한 결과는 아닙니다.
- **개발 중:** core/adapter 분리를 다루는 [0.2 PR #62](https://github.com/ym0506/routecontract/pull/62)는 미출시 작업이며 현재 지원 범위에 포함하지 않습니다.

<sub>상태 확인: 2026-09-08. 소스·반복 검증은 v0.1.3 commit f1efd71, Central 게시 기록은 commit 961c4ba에 고정했습니다.</sub>
