# Apache ShardingSphere 기여

[English](./SHARDINGSPHERE_CONTRIBUTIONS.md) · [프로필로 돌아가기](./README.ko.md) · [전체 오픈소스 기여](./CONTRIBUTIONS.ko.md)

**ShardingSphere 병합 PR 11건:** 런타임 코드 개선 7건, CI 개선 1건, 회귀 테스트 1건, 문서 2건입니다. OpenTelemetry와 go-mysql-server를 포함한 외부 오픈소스 병합은 **[총 13건](./CONTRIBUTIONS.ko.md)**입니다. 상태 확인: 2026-09-14.

## 대표 기여

### 인덱스 이름의 길이 제약과 논리 이름 복원

샤딩 과정에서 길어진 인덱스 이름이 DB의 식별자 제약을 초과하는 문제를 해결했습니다. 기존 이름이 제약 안에 들어가면 유지하고, 초과하면 같은 입력에 항상 같은 값을 내는 해시 접미사를 붙이고 UTF-8 바이트 단위로 이름을 잘랐습니다. [PR #38449](https://github.com/apache/shardingsphere/pull/38449)

**구현 범위: 변경 파일 36개 = 실행 Java 코드 23개 + 테스트·테스트 리소스 13개.** DDL 재작성·메타데이터 갱신·파이프라인 경로에 걸친 변경입니다. 파일 수는 구현 범위를, 아래 회귀 예제는 실제 수정된 동작을 보여 줍니다. [변경 파일](https://github.com/apache/shardingsphere/pull/38449/files)

이름 생성 이후의 메타데이터 복원도 함께 처리했습니다. 새 테이블은 기존 논리 메타데이터가 없으므로 `CREATE TABLE` 문에서 이름 후보를 얻고 실제 생성 규칙과 일치하는 후보로 원래 논리 이름을 복원합니다. 이 경로를 SQL 재작성·메타데이터 갱신·파이프라인에 연결했습니다.

**PostgreSQL 회귀 예제: 85 → 63 UTF-8 바이트.** 같은 테스트 입력에 기존 생성 공식을 적용하면 85바이트이며, 개선된 결과는 63바이트로 검증됩니다. 특정 인덱스 이름의 길이 비교입니다. [테스트 입력과 결과 검증](https://github.com/apache/shardingsphere/blob/b41c843dfefe41038851d83db5b1e2a47ca88b39/infra/common/src/test/java/org/apache/shardingsphere/infra/metadata/database/schema/util/IndexMetaDataUtilsTest.java#L120-L125)

### 빈 입력에서 잘못 생성되던 NULL 행 수정

PostgreSQL·openGauss의 윈도 집계가 빈 샤딩 입력에서 **NULL 1행을 반환하던 오류를 올바른 0행으로 수정**했습니다. [PR #38659](https://github.com/apache/shardingsphere/pull/38659)

원인은 파서에서 유실된 `OVER` 정보였습니다. 이 정보가 사라지면 바인더가 윈도 집계를 일반 집계로 분류하고 결과 병합 단계가 합성 행을 만들었습니다. 파서·바인더의 정보 전달을 수정하고 열 이름과 빈 결과 병합까지 회귀 검증했습니다.

### 상관 서브쿼리의 잘못된 실행 계획 수정

SQL Federation에서 외부 쿼리의 열을 참조하는 `IN` 서브쿼리가 컴파일에 실패하는 문제를 해결했습니다. 상관 참조를 포함한 식이 스캔 내부로 잘못 내려가는 지점을 찾아 차단했습니다. [PR #38405](https://github.com/apache/shardingsphere/pull/38405)

중첩된 식의 외부 참조까지 검사하되 일반 프로젝션을 하위 단계로 내려보내는 기존 최적화는 유지합니다. 규칙 단위·컴파일 통합 검증과 MySQL/PostgreSQL/openGauss를 대상으로 하는 SQL E2E 사례를 추가했습니다.

### 호환용 결과를 반환할 때 누락되던 SQL 실패 알림

**2026년 9월 14일 병합** · [PR #39764](https://github.com/apache/shardingsphere/pull/39764)

RouteContract의 `SQLExecutionHook` 종료 처리를 점검하다, JDBC 실행이 실패한 뒤 호환용 결과를 반환하면서 훅에 종료 알림을 보내지 않는 경로를 발견했습니다. `finishFailure`를 호환 처리 앞으로 옮겨 원래 SQL 예외를 전달하고, 기존 trunk·worker 반환값과 일반 예외 전파 동작은 유지했습니다. [발견 경위와 수정 전 실패 재현](https://github.com/apache/shardingsphere/issues/39763)

**구현 범위: 운영 코드 1개와 테스트 1개, 총 2개 파일.** 운영 코드에서는 기존 콜백 호출의 위치를 옮겼습니다. 특정 경로의 실행 상태를 정확히 알리도록 고친 신뢰성 개선입니다.

- **두 실행 경로 검증:** 기존 테스트를 trunk·worker 매개변수화 사례로 보강해 반환값, 원래 예외, 콜백 순서와 실패 알림 1회 전달을 확인했습니다. [회귀 테스트](https://github.com/apache/shardingsphere/blob/b13ad32b12ceb92868445c98dcc811dadf39ecc5/infra/executor/src/test/java/org/apache/shardingsphere/infra/executor/sql/execute/engine/driver/jdbc/JDBCExecutorCallbackTest.java#L74-L102)
- **실행기 전체 테스트 196건 통과:** 공개 CI 기록이며 이번 회귀 사례와 기존 테스트를 포함한 수치입니다. 새 테스트 196개를 추가했다는 뜻은 아닙니다. [CI 실행](https://github.com/apache/shardingsphere/actions/runs/34171532124) · [메인테이너 승인](https://github.com/apache/shardingsphere/pull/39764#pullrequestreview-5195469387)

대상은 ShardingSphere 실행기의 이종 DB 호환 처리 경로입니다. 단위 테스트로 검증했으며 별도의 실제 이종 DB·Proxy 환경 재현은 수행하지 않았습니다. RouteContract가 지원하는 ShardingSphere-JDBC 5.5.3·MySQL 환경에서도 같은 문제가 발생한다고 확인한 것은 아닙니다.

## 병합된 ShardingSphere PR 전체

| 분류 | PR | 결과 |
|---|---|---|
| 런타임 | [#39764](https://github.com/apache/shardingsphere/pull/39764) | 호환용 결과 반환 전 SQL 실패 알림 누락 수정. 기존 trunk·worker 반환 동작 유지 |
| 런타임 | [#38449](https://github.com/apache/shardingsphere/pull/38449) | 인덱스 이름 길이 제약 충족과 논리 메타데이터 복원 |
| 런타임 | [#38659](https://github.com/apache/shardingsphere/pull/38659) | PostgreSQL/openGauss 윈도 집계의 빈 결과 정확성 회복 |
| 런타임 | [#38405](https://github.com/apache/shardingsphere/pull/38405) | 상관 IN 서브쿼리에서 프로젝션을 하위 단계로 잘못 내려보내는 문제 수정 |
| 런타임 | [#38327](https://github.com/apache/shardingsphere/pull/38327) | 선택형 HASH_MOD 정규화로 Integer·Long·BigInteger **3종**에서 정수 범위의 같은 값이 같은 샤드로 가도록 수정. 기존 동작은 기본값으로 유지 |
| 런타임 | [#38187](https://github.com/apache/shardingsphere/pull/38187) | PostgreSQL 단항 NOT을 AST에 보존해 내부 서브쿼리의 샤딩 테이블 추출 가능 |
| 런타임 | [#38223](https://github.com/apache/shardingsphere/pull/38223) | MySQL 제약 메타데이터 조회에 TABLE_SCHEMA 조건 추가 |
| CI | [#38352](https://github.com/apache/shardingsphere/pull/38352) | 후속 E2E 작업에서 동일 소스 스냅샷 재사용, 불필요한 문서 파일 제외 |
| 회귀 테스트 | [#38685](https://github.com/apache/shardingsphere/pull/38685) | 암호화 규칙 삭제·동일 이름 재생성·삭제 후 DML 검증. 런타임 코드 변경 없음 |
| 문서 | [#38206](https://github.com/apache/shardingsphere/pull/38206) | DistSQL 3개 명령의 영어·중국어 문서 **6페이지** 추가 |
| 문서 | [#39535](https://github.com/apache/shardingsphere/pull/39535) | 타인이 구현한 카테시안 라우팅 수정의 릴리스 노트 누락 보완 |

## CI 아카이브 크기

**CI 정량 사례:** #38352의 새 소스 아카이브에서 불필요한 문서 파일을 제외했습니다. 로컬에서 비교한 아카이브 크기는 **약 109MB → 5.1MB, 약 95% 축소**됐습니다. 원 수치가 근삿값인 아카이브 크기 비교이며, CI 실행 시간·실패율 개선률은 측정하지 않았습니다. [공개 비교 기록과 명령](https://github.com/apache/shardingsphere/pull/38352#issuecomment-4011386704)

## 다른 프로젝트의 병합 기여

**go-mysql-server:** 시스템 변수 숫자 타입 **4종**에 `NumberType`을 구현해 숫자 타입 판별과 인터페이스 호환성을 개선했습니다. 컴파일 시 인터페이스 검증과 숫자·문자열 타입 판별 검증을 추가했습니다. [병합 PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442)
