# Apache ShardingSphere 기여

[English](./SHARDINGSPHERE_CONTRIBUTIONS.md) · [프로필로 돌아가기](./README.ko.md)

2026-08-31 기준 **병합 PR 10건**: production code 수정·개선 6건, CI 신뢰성 개선 1건, 회귀 검증 1건, 문서·release note 2건입니다.

## Production Code

| PR | 문제와 해결 결과 | 근거 |
|---|---|---|
| [#38187: PostgreSQL scalar subquery table extraction에서 unary `NOT` 보존](https://github.com/apache/shardingsphere/pull/38187) | Unary `NOT`이 common expression으로 처리되면서 nested sharding table이 extraction과 rewrite에서 누락될 수 있었습니다. `NotExpression`으로 보존하고, 처음의 넓은 조건이 `IS NOT`에 영향을 주지 않도록 범위를 좁혔습니다. | 2 files · PostgreSQL parser IT 1,147개 통과 보고 · checks 148건 |
| [#38223: MySQL constraint metadata를 schema 범위로 제한](https://github.com/apache/shardingsphere/pull/38223) | `KEY_COLUMN_USAGE` 조회에 `TABLE_SCHEMA = ?`를 추가해 metadata refresh가 관계없는 schema까지 조회하지 않도록 했습니다. | 2 files · checks 148건 |
| [#38327: Upgrade-safe HASH_MOD numeric normalization](https://github.com/apache/shardingsphere/pull/38327) | 같은 음수 값도 `Integer`, `Long`, `BigInteger`에 따라 다른 shard로 갈 수 있었습니다. Normalization을 opt-in으로 추가하고 legacy routing을 기본값으로 유지했으며 integer boundary와 범위 밖 값을 검증했습니다. | 6 files · checks 82건 |
| [#38405: SQL Federation project pushdown의 correlation guard](https://github.com/apache/shardingsphere/pull/38405) | Correlated expression이 `LogicalScan` 안으로 pushdown되는 것을 막고 정상적인 non-correlated pushdown은 유지했습니다. | Targeted tests 114개 · MySQL/PostgreSQL/openGauss E2E · checks 141건 |
| [#38449: Generated sharding index 이름의 length-safety와 recovery](https://github.com/apache/shardingsphere/pull/38449) | Legacy-first deterministic naming과 candidate로 검증하는 logical-name recovery를 rewrite와 metadata lifecycle 전반에 적용했습니다. | 36 files · +1,383/-74 · 12 commits · change request 5회 · checks 87건 |
| [#38659: Window aggregate의 empty-input cardinality 보존](https://github.com/apache/shardingsphere/pull/38659) | PostgreSQL/openGauss의 `OVER` 정보를 parser와 binder에서 보존해 입력 0행의 window aggregate가 합성된 `NULL` 1행이 아닌 0행을 반환하도록 했습니다. | 10 files · parser/binder/merge regression · checks 79건 |

## CI 신뢰성

| PR | 문제와 해결 결과 | 근거 |
|---|---|---|
| [#38352: E2E-SQL job에서 하나의 source snapshot 재사용](https://github.com/apache/shardingsphere/pull/38352) | Downstream job이 사라질 수 있는 synthetic PR merge ref를 늦게 checkout하던 구조를, 한 번 만든 source snapshot을 matrix job이 재사용하는 구조로 바꿨습니다. 불필요한 `docs/`를 제외해 로컬 측정 archive가 약 109MB에서 5.1MB로 줄었습니다. | Workflow 1 file · local archive 95.3% 감소 · checks 89건 통과/2건 skip |

95.3%는 archive 크기 측정값이며 workflow 실행시간이나 실패율이 같은 비율로 개선되었다는 주장이 아닙니다.

## 회귀 검증

| PR | 문제와 해결 결과 | 근거 |
|---|---|---|
| [#38685: Encrypt rule drop 이후 recreate와 runtime behavior 검증](https://github.com/apache/shardingsphere/pull/38685) | Named rule-item 삭제, 같은 이름으로 recreate, `LOAD SINGLE TABLE`, drop 이후 DML rewrite behavior를 검증했습니다. Production code를 변경하지 않은 test-only 기여입니다. | 5 files · approval 전 maintainer change request 4회 · checks 64건 통과/2건 skip |

## 문서와 Release 완전성

| PR | 결과 | 근거 |
|---|---|---|
| [#38206: 누락된 DistSQL RAL 문서 추가](https://github.com/apache/shardingsphere/pull/38206) | `EXPORT METADATA`, `IMPORT METADATA`, `EXPORT STORAGE NODES`의 영문·중문 syntax page를 추가했습니다. | 6 pages · 344 added lines |
| [#39535: Cartesian routing fix를 5.5.4 release note에 추가](https://github.com/apache/shardingsphere/pull/39535) | 이미 merge된 routing correction의 누락된 release-note entry를 추가했습니다. | Release-note 1 line · checks 4건 |

## 수치 해석 원칙

- File과 commit 수는 변경 범위의 근거이며 사용자 영향 수치가 아닙니다.
- Check 수는 test-case 수가 아니라 GitHub check run 수입니다.
- Targeted test 수는 명시된 검증 명령의 범위이며 전체 프로젝트 test suite 수가 아닙니다.
- 직접 측정하지 않은 사용자 수, production incident 감소율, latency 또는 throughput 개선은 주장하지 않습니다.
