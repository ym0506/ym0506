# 주요 엔지니어링 작업

[English](./README.md)

## 한눈에 보기

- **Apache ShardingSphere:** parser, SQL federation, sharding, metadata, CI 신뢰성, 회귀 검증, release 문서에 걸친 [병합 PR 10건](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)
- **RouteContract:** [v0.1.2](https://github.com/ym0506/routecontract/releases/tag/v0.1.2), **exact tag 기준 tests 52개**, **deterministic capture 160회**, **직접 검증 가능한 release assets 12개**

## 대표 프로젝트: RouteContract

**업무 결과가 같아도 달라질 수 있는 ShardingSphere-JDBC의 물리 실행 구조를 CI에서 검출하는 contract입니다.**

실제 MySQL fixture에서 업무 결과는 그대로 유지하면서, hook이 보고한 JDBC 실행 시도와 data-source alias가 **1에서 2**로 바뀌는 상황을 재현했습니다. RouteContract는 이 구조적 차이를 값이 제거된 manifest로 기록하고, 승인되지 않은 변경을 stable diagnostics와 함께 거부합니다.

`관측 실행 시도 1 → 2` · `deterministic captures 160회` · `v0.1.2 tests 52개`

[저장소](https://github.com/ym0506/routecontract) · [2분 54초 시연](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [Release](https://github.com/ym0506/routecontract/releases/tag/v0.1.2) · [기술 명세](https://github.com/ym0506/routecontract/blob/main/docs/specification.md)

판정 범위는 hook-reported execution attempts이며, complete route plan과 transaction outcome은 포함하지 않습니다. 정확한 범위는 [기술 명세](https://github.com/ym0506/routecontract/blob/main/docs/specification.md)에 기록했습니다.

## 주요 Apache ShardingSphere 기여

### [#38449 · Sharding index 이름 정확성과 호환성](https://github.com/apache/shardingsphere/pull/38449)

문자열 길이 문제가 metadata identity와 호환성 문제로 이어지는 결함을 해결했습니다. 안전한 경우 legacy physical 이름을 유지하고, 필요한 경우 deterministic length-safe fallback을 사용하며, 신규 `CREATE TABLE`을 포함한 DDL rewrite와 metadata refresh 경로에서 logical 이름을 복원하도록 개선했습니다.

**범위:** `36 files` · `12 commits` · `maintainer change request 5회` · `checks 87건 통과`

### [#38659 · PostgreSQL/openGauss window aggregate cardinality](https://github.com/apache/shardingsphere/pull/38659)

사라진 `OVER` 정보 때문에 merge 계층이 잘못된 aggregate 1행을 생성하는 결함을 해결했습니다. Parser와 binder에서 window semantics를 보존해 empty input의 결과를 잘못된 **`NULL` 1행에서 올바른 0행**으로 수정했습니다.

**범위:** `parser → binder → merge` · `10 files` · `2 dialects` · `checks 79건 통과`

### [#38405 · Correlated subquery planner 정확성](https://github.com/apache/shardingsphere/pull/38405)

Correlated outer reference가 `LogicalScan` 안으로 pushdown되는 것을 막고, correlation이 없는 정상적인 project pushdown은 유지했습니다.

**검증:** `targeted tests 114개` · `MySQL, PostgreSQL, openGauss shared E2E` · `checks 141건 통과`

**다른 기여:** [#38327 · HASH_MOD upgrade 호환성](https://github.com/apache/shardingsphere/pull/38327) · [병합 PR 10건 전체 보기](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)

<sub>수치는 2026-09-03에 확인했습니다. GitHub check 수는 test-case 수가 아니라 check run 수입니다.</sub>
