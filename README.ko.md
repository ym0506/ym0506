# Java · SQL · 데이터 정확성

SQL 오류를 재현하고 실행 경로를 추적해 Apache ShardingSphere에 수정 사항을 기여합니다. SQL 실행의 회귀를 CI에서 찾는 Java 라이브러리 **RouteContract**를 개발했습니다.

[English](./README.md) · [오픈소스 기여 상세](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) · [프로젝트 검증 결과](./ROUTECONTRACT.ko.md)

## Apache ShardingSphere — 병합된 기여 10건

런타임 코드 개선 6건, CI 개선 1건, 회귀 테스트 1건, 문서 2건입니다. 대표 사례는 다음과 같습니다.

- **인덱스 이름의 길이 제약 초과와 메타데이터 복원 문제 수정.** DB의 이름 길이 제약을 충족하도록 생성 규칙을 개선하고, 기존 이름과 논리 이름 복원을 함께 고려했습니다. [PR #38449](https://github.com/apache/shardingsphere/pull/38449)
- **빈 테이블에서 잘못 반환되던 NULL 1행을 정상적인 0행으로 수정.** PostgreSQL·openGauss의 윈도 함수 정보를 parser와 binder에서 보존해 결과 병합 오류를 해결했습니다. [PR #38659](https://github.com/apache/shardingsphere/pull/38659)
- **상관 서브쿼리의 실행 계획 오류 수정.** 외부 쿼리 참조가 있는 식의 잘못된 pushdown을 막고, 일반 쿼리의 기존 최적화는 유지했습니다. [PR #38405](https://github.com/apache/shardingsphere/pull/38405)

다른 기여: [HASH_MOD 숫자 타입 간 라우팅 일관성](https://github.com/apache/shardingsphere/pull/38327) · [go-mysql-server 시스템 숫자 타입 지원](https://github.com/dolthub/go-mysql-server/pull/3442)

## RouteContract — 같은 조회 결과 뒤에 숨은 DB 실행 변화 검출

기존 결과 검증에 **DB 실행 구조 검증**을 더하는 Java 테스트 라이브러리입니다.

- **문제와 결과:** 실제 MySQL 예제에서 같은 조회 결과를 유지한 채 관측 실행 시도가 **1 → 2회**로 늘어나는 변경을 재현하고, 승인된 기준을 넘으면 CI를 실패시킵니다.
- **구현:** 실행 횟수·데이터 소스·재작성 SQL 구조를 비교하고, 변경 내용을 Markdown·JSON 리포트로 제공합니다.
- **배포와 검증:** **v0.1.3 Maven Central 배포**, 릴리스 Java/MySQL 테스트 **62개 통과**. [수치·조건·근거](./ROUTECONTRACT.ko.md)

[저장소·설치](https://github.com/ym0506/routecontract) · [2분 54초 데모](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [CI 리포트 예시](https://github.com/ym0506/routecontract/blob/main/docs/evidence/ci-review-report-example.md)

<sub>지원: Java 17 · ShardingSphere-JDBC 5.5.3 · 동기식·비배치 PreparedStatement. 실행 시도는 SQLExecutionHook 관측값이며, 1 → 2는 회귀 재현 수치입니다. 성능 향상률이나 운영 처리량을 뜻하지 않습니다. 기여·배포 상태 확인: 2026-09-08.</sub>
