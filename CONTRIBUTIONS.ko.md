# 오픈소스 기여

[프로필](./README.ko.md) · [English](./CONTRIBUTIONS.md)

**외부 프로젝트 4곳에 PR 14건 병합.** 2026년 10월 26일 확인했습니다.

**런타임 코드 변경 10건**, CI 개선 1건, 회귀 테스트 1건, 문서 2건입니다. 실제 제품 동작을 바꾼 기여와 이를 뒷받침하는 작업을 구분했습니다.

| 프로젝트 | 병합 | 기여와 근거 |
| --- | --- | --- |
| Apache Fineract | **1건** | 코어뱅킹 돈 계산 정확성: 원천징수·수수료 세금을 `double` 대신 `BigDecimal`로 정확히 계산하도록 수정하고 회귀 테스트 6개 추가. CI 중 발견한 불안정 E2E 테스트도 보고([FINERACT-2989](https://issues.apache.org/jira/browse/FINERACT-2989), 병합 수 제외). [PR #6731](https://github.com/apache/fineract/pull/6731) · [문제·수정·검증](./FINERACT_CONTRIBUTIONS.ko.md) |
| Apache ShardingSphere | **11건** | SQL 정확성, 인덱스 이름 호환성, 선택형 숫자 라우팅, 실행 훅의 신뢰성, CI·테스트·문서. [전체 PR 목록과 상세 사례](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md) |
| OpenTelemetry Java instrumentation | **1건** | JDBC SQL 마스킹 설정과 인스턴스별 호환성. [PR #20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020) · [구현과 검증](./OPENTELEMETRY_CONTRIBUTIONS.ko.md) |
| go-mysql-server | **1건** | 시스템 숫자 타입 **4종**에 `NumberType` 구현. 컴파일 시 인터페이스 검증과 숫자·문자열 타입 판별 검증 추가. [PR #3442](https://github.com/dolthub/go-mysql-server/pull/3442) |

## 기술적으로 다룬 문제

- **돈 계산 정확성:** 반올림 경계에 걸린 세액이 `double` 계산 때문에 1단위 적게 나오던 문제를 `BigDecimal` 정확 계산과 1회 반올림으로 수정. [#6731](https://github.com/apache/fineract/pull/6731)
- **여러 구성 요소에 걸친 변경:** DDL 재작성·메타데이터 갱신·파이프라인 경로를 연결한 인덱스 이름 생성과 논리 이름 복원. [#38449](https://github.com/apache/shardingsphere/pull/38449)
- **호환성 판단:** HASH_MOD 정규화는 선택형으로 추가하고 기존 라우팅 기본값을 보존. JDBC SQL 마스킹은 설정 우선순위와 인스턴스 경계를 반영. [#38327](https://github.com/apache/shardingsphere/pull/38327) · [#20020](https://github.com/open-telemetry/opentelemetry-java-instrumentation/pull/20020)
- **결과 정확성:** 빈 입력의 의미를 유지하도록 윈도 함수 정보를 보존하고, 상관 참조를 포함한 식이 실행 계획의 하위 단계로 잘못 내려가지 않도록 수정. [#38659](https://github.com/apache/shardingsphere/pull/38659) · [#38405](https://github.com/apache/shardingsphere/pull/38405)

- **의존 라이브러리 조사:** RouteContract의 실행 훅을 점검하다 실패 알림 누락을 발견해 수정하고 병합했습니다. 회귀 테스트로 호환용 반환값 유지와 실패 알림 1회 전달을 확인했습니다. [#39764](https://github.com/apache/shardingsphere/pull/39764)

파일 수는 변경 범위를, 바이트·행·타입 수는 고친 동작을, 테스트 수는 당시 검증 범위를 나타냅니다. 성능이나 운영 도입은 측정·근거가 있는 범위에서만 설명합니다.

[RouteContract](./ROUTECONTRACT.ko.md)는 직접 개발·배포한 별도 라이브러리이며, 해당 저장소의 PR은 외부 프로젝트의 병합 수에 포함하지 않았습니다.
