# 오픈소스 기여

[프로필](./README.ko.md) · [English](./CONTRIBUTIONS.md)

**외부 프로젝트 3곳에 PR 12건 병합.** 2026년 9월 14일 확인했습니다.

**런타임 코드 변경 8건**, CI 개선 1건, 회귀 테스트 1건, 문서 2건입니다. 실제 제품 동작을 바꾼 기여와 이를 뒷받침하는 작업을 구분했습니다.

| 프로젝트 | 병합 | 기여와 근거 |
| --- | --- | --- |
| Apache ShardingSphere | **10건** | SQL 정확성, 인덱스 이름 호환성, 선택형 숫자 라우팅, CI·테스트·문서. [전체 PR 목록과 상세 사례](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) |
| OpenTelemetry Java instrumentation | **1건** | JDBC SQL 마스킹 설정과 인스턴스별 호환성. [PR #20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) · [구현과 검증](./OPENTELEMETRY_CONTRIBUTIONS.ko.md) |
| go-mysql-server | **1건** | 시스템 숫자 타입 **4종**에 `NumberType` 구현. 컴파일 시 인터페이스 검증과 숫자·문자열 타입 판별 검증 추가. [PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442) |

## 기술적으로 다룬 문제

- **여러 구성 요소에 걸친 변경:** DDL 재작성·메타데이터 갱신·파이프라인 경로를 연결한 인덱스 이름 생성과 논리 이름 복원. [#38449](https://github.com/apache/shardingsphere/pull/38449)
- **호환성 판단:** HASH_MOD 정규화는 선택형으로 추가하고 기존 라우팅 기본값을 보존. JDBC SQL 마스킹은 설정 우선순위와 인스턴스 경계를 반영. [#38327](https://github.com/apache/shardingsphere/pull/38327) · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)
- **결과 정확성:** 빈 입력의 의미를 유지하도록 윈도 함수 정보를 보존하고, 상관 참조를 포함한 식이 실행 계획의 하위 단계로 잘못 내려가지 않도록 수정. [#38659](https://github.com/apache/shardingsphere/pull/38659) · [#38405](https://github.com/apache/shardingsphere/pull/38405)

파일 수는 변경 범위를, 바이트·행·타입 수는 고친 동작을, 테스트 수는 당시 검증 범위를 나타냅니다. 성능이나 운영 도입은 측정·근거가 있는 범위에서만 설명합니다.

## 진행 중인 조사

아래 항목은 병합 PR 12건과 별도입니다.

- **ShardingSphere 훅의 수명주기:** 호환성을 위한 대체 경로로 넘어가기 전에 종료 콜백이 누락되는 문제를 수정해 PR을 제출했습니다. 수정 전 실패하는 회귀 테스트를 포함했습니다. [PR #39764 — 진행 중·미병합](https://github.com/apache/shardingsphere/pull/39764)
- **ShardingSphere 에이전트의 스팬 손실:** JDBC 콜백이 겹칠 때 실행 스팬이 누락되는 문제를 재현해 보고했습니다. [이슈 #39765 — 진행 중](https://github.com/apache/shardingsphere/issues/39765)

[RouteContract](./ROUTECONTRACT.ko.md)는 직접 개발·배포한 별도 라이브러리이며, 해당 저장소의 PR은 외부 프로젝트의 병합 수에 포함하지 않았습니다.
