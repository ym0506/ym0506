# Java · SQL 엔진 · 개발 도구

Apache ShardingSphere의 SQL 엔진을 개선하고, 실행 회귀를 CI에서 찾는 Java 라이브러리 **RouteContract**를 개발합니다.

**[외부 upstream 병합 PR 11건](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)** · **[RouteContract Maven Central 배포](./ROUTECONTRACT.ko.md)**<br>
[English](./README.md)

## Apache ShardingSphere — 병합 PR 10건

**런타임 코드 개선 6건**과 CI·회귀 테스트·문서 기여가 포함됩니다.

**인덱스 이름 — 이름 생성·호환성 개선** · [#38449](https://github.com/apache/shardingsphere/pull/38449)<br>
**범위: 변경 파일 36개.** DDL 재작성·메타데이터 갱신·pipeline 경로의 구현과 테스트를 포함합니다.<br>
**결과: PostgreSQL 회귀 예제에서 85 → 63 UTF-8 바이트.** 길이 제약을 충족하는 이름 생성과 논리 이름 복원을 구현하고, 제약 안에 드는 기존 물리 이름은 유지했습니다.

**HASH_MOD — 선택적으로 활성화하는 라우팅 기능 추가** · [#38327](https://github.com/apache/shardingsphere/pull/38327)<br>
**숫자 타입 3종: Integer·Long·BigInteger.** 부호 있는 32비트 정수 범위의 같은 값이 타입에 관계없이 같은 shard로 가도록 정규화를 추가하고, 업그레이드 호환성을 위해 기존 라우팅을 기본값으로 유지했습니다.

**윈도 집계 — SQL 결과 정확성 수정** · [#38659](https://github.com/apache/shardingsphere/pull/38659)<br>
**SQL 방언 2종 · 잘못된 NULL 1행 → 올바른 0행.** PostgreSQL·openGauss의 윈도 함수 정보를 parser·binder에서 보존해 빈 입력의 결과를 바로잡았습니다.

다른 사례: [상관 서브쿼리 실행 계획 수정](https://github.com/apache/shardingsphere/pull/38405) · [CI 아카이브: 로컬 비교 약 109 → 5.1MB](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md#ci-아카이브-크기)<br>
[전체 기여·범위·근거](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) — go-mysql-server 숫자 타입 4종의 호환성 개선·병합도 포함합니다.

## RouteContract — SQL 실행 회귀 검증

**배포된 Java 라이브러리 · v0.1.3 · Maven Central**

- **검출한 회귀:** 실제 MySQL 예제에서 업무 결과가 같은데 관측 JDBC 실행 시도가 **1 → 2회**로 늘어나는 변경을 재현하고, 승인된 기준을 넘으면 CI에서 거부합니다.
- **라이브러리 기능:** operation별 수집, 실행 예산, 재작성 SQL 검사, Markdown·JSON 검토 리포트.
- **검증 결과:** **v0.1.3 Java/MySQL 테스트 62개 통과**. **고정 사례 8개 × 20회 = 160회 capture**에서 사례별 구조적 signature 1개를 확인했습니다.

[설치](https://github.com/ym0506/routecontract#install-013) · [2분 54초 데모](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [리포트 예시](https://github.com/ym0506/routecontract/blob/main/docs/evidence/ci-review-report-example.md) · [검증 조건과 근거](./ROUTECONTRACT.ko.md)

<sub>지원: Java 17 · 정확히 ShardingSphere-JDBC 5.5.3 · 동기식·비배치 PreparedStatement. 실행 횟수는 통제된 예제에서 hook으로 관측한 실행 시도입니다. 상태 확인: 2026-09-08.</sub>
