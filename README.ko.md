# 주요 엔지니어링 작업

[English](./README.md)

| Apache ShardingSphere upstream | RouteContract release |
|:---:|:---:|
| **병합 PR 10건** | **v0.1.2** |
| production code PR 6건 · CI 신뢰성 PR 1건 · 회귀 검증 PR 1건 · 문서/release PR 2건 | 7 suites · exact-tag tests 52개 · 공개 release assets 12개 |

## Apache ShardingSphere

| 기여와 해결 결과 | 범위와 검증 근거 |
|---|---|
| [**#38449: Sharding index 이름 정확성과 호환성**](https://github.com/apache/shardingsphere/pull/38449)<br><br>문자열 길이 문제가 metadata identity와 호환성 문제로 이어졌습니다. 안전한 경우 legacy physical 이름을 유지하고, 필요한 경우 deterministic length-safe fallback을 사용하며, 신규 `CREATE TABLE`을 포함한 DDL rewrite와 metadata refresh 경로에서 logical 이름을 복원하도록 개선했습니다. | **36 files**<br>**12 commits**<br>**maintainer change request 5회**<br>**checks 87건 통과** |
| [**#38659: PostgreSQL/openGauss window aggregate cardinality**](https://github.com/apache/shardingsphere/pull/38659)<br><br>사라진 `OVER` 정보 때문에 merge 계층이 잘못된 aggregate 1행을 만들었습니다. Parser와 binder에서 window semantics를 보존해 empty input의 결과를 잘못된 **`NULL` 1행에서 올바른 0행**으로 수정했습니다. | Parser → binder → merge regression<br>**10 files** · **2 dialects**<br>**checks 79건 통과** |
| [**#38405: Correlated subquery planner 정확성**](https://github.com/apache/shardingsphere/pull/38405)<br><br>Correlated outer reference가 `LogicalScan` 안으로 pushdown되는 것을 막고, correlation이 없는 정상적인 project pushdown은 유지했습니다. | **Targeted tests 114개**<br>MySQL/PostgreSQL/openGauss shared E2E<br>**checks 141건 통과** |
| [**#38327: HASH_MOD upgrade 호환성**](https://github.com/apache/shardingsphere/pull/38327)<br><br>같은 음수 값을 `Integer`, `Long`, `BigInteger`로 표현할 때의 normalization을 opt-in으로 추가하고, upgrade 후 기존 데이터의 shard 위치가 바뀌지 않도록 legacy routing을 기본값으로 유지했습니다. | Legacy/normalized mode<br>Boundary/out-of-range coverage<br>**checks 82건 통과** |

[Apache ShardingSphere 병합 PR 10건 전체 보기](./SHARDINGSPHERE_CONTRIBUTIONS.ko.md)

## RouteContract

[RouteContract](https://github.com/ym0506/routecontract)는 ShardingSphere-JDBC의 physical execution 변화를 사람이 검토할 수 있는 CI contract로 만듭니다.

기능 assertion은 같은 업무 결과를 반환하더라도 hook이 보고한 물리 JDBC 실행 시도와 data-source alias가 **1에서 2**로 달라질 수 있습니다. 실제 MySQL로 검증한 fixture는 이 차이를 값이 제거된 manifest로 기록하고, 승인되지 않은 candidate를 stable `RCM201`·`RCM202` 진단과 함께 거부합니다.

| 기능 | 검증 근거 |
|---|---|
| 하나의 application operation을 capture하고 관측 실행 구조를 사람이 승인한 baseline과 비교 | Real-MySQL Quick Start: 업무 결과는 동일하지만 관측 시도와 data source가 **1 → 2** |
| Source control과 CI review에 사용할 수 있도록 structural output을 deterministic하게 유지 | 8개 case × 20회 = **160 captures**, case별 structural signature 1개 |
| 재현 가능한 release boundary 제공 | `v0.1.2`: **7 suites, 52 tests, failures/errors/skips 0건** |
| Release 구성 요소를 직접 검증할 수 있게 공개 | JAR, POM, source, Javadoc, SBOM, checksum, supply-chain evidence를 포함한 **assets 12개** |

[저장소](https://github.com/ym0506/routecontract) · [2분 54초 시연](https://www.youtube.com/watch?v=pcgvNNxd1mM) · [v0.1.2 release](https://github.com/ym0506/routecontract/releases/tag/v0.1.2) · [기술 명세](https://github.com/ym0506/routecontract/blob/main/docs/specification.md)

이 contract는 의도적으로 hook-reported execution attempts만 관측합니다. Complete route plan과 transaction outcome은 판정 범위 밖이며, 정확한 경계는 기술 명세에 기록했습니다.

<sub>위 GitHub check 수는 test-case 수가 아니라 check run 수입니다. 기여 수는 2026-08-31 기준입니다.</sub>
