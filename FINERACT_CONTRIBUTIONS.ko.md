# Apache Fineract 기여

[프로필](./README.ko.md) · [전체 기여](./CONTRIBUTIONS.ko.md) · [English](./FINERACT_CONTRIBUTIONS.md)

## 원천징수 세액이 1원 덜 계산되던 문제 — 돈 계산 정확성

**병합 완료** · [이슈 FINERACT-2988](https://issues.apache.org/jira/browse/FINERACT-2988) → [PR #6731](https://github.com/apache/fineract/pull/6731) · 2026년 10월 26일(UTC)

Apache Fineract는 대출·예금·회계를 다루는 오픈소스 코어뱅킹 플랫폼입니다. `TaxUtils.splitTax`와 `splitTaxData`는 **예금 이자 원천징수**와 **수수료 세금**의 세액을 계산합니다. 두 메서드는 금액에 세율을 `double`로 곱한 뒤, 설정된 반올림 방식으로 반올림했습니다.

정확한 값이 반올림 경계에 걸리면 `double` 값은 경계보다 아주 조금 작아집니다. 그래서 반올림 방향이 바뀌고 세금이 최소 단위 하나만큼 덜 계산됐습니다.

| 금액 × 세율 | 정확한 값 | 반올림 | 기대값 | 수정 전 |
|---|---|---|---|---|
| 4.10 × 15% | 0.615 | HALF_EVEN, 소수 2자리 | 0.62 | **0.61** |
| 5,250 × 1.4% | 73.5 | HALF_EVEN, 소수 0자리 | 74 | **73** |
| 5,500 × 1.4% | 77 | DOWN, 소수 0자리 | 77 | **76** |

1~1,000,000 정수 금액을 세율 1.4%로 전수 검사해 보니 **HALF_EVEN에서 187개**, HALF_UP에서 374개 금액이 틀렸습니다. 반면 10%·14%·16% 같은 세율에서는 차이가 없었습니다. 그래서 1,000 × 16% 같은 기존 테스트로는 잡히지 않았습니다. 이 `double` 계산은 2016년 예금 원천징수 기능이 처음 들어올 때부터 있던 코드입니다.

수정은 `금액 × 세율 / 100`을 `BigDecimal`로 정확히 계산한 뒤 요청된 자릿수로 한 번만 반올림하게 했습니다. 정확한 값이 반올림 경계에 걸리는 경우만 결과가 바뀝니다.

### 범위와 검증

- **변경 파일 2개:** 운영 코드 1개(+7 / −10줄), 새 테스트 클래스 1개
- **새 회귀 테스트 6개**(`TaxUtilsTest`): 두 메서드 모두, HALF_EVEN 경계, 소수점 없는 통화, 정확히 떨어지는 금액의 DOWN을 다룹니다. **수정 전에는 6개 중 5개가 실패**했고, 수정 후에는 모두 통과합니다.
- 모듈 검사 통과: `fineract-tax` 테스트 17개, 포맷·스타일 검사, 예금 원천징수 단위 테스트
- **메인테이너 검증:** 리뷰어는 머지 전에 세 사례를 직접 재현했습니다. 또 금액 0.01~2,000.00, 세율 0.5~30%, 반올림 방식 3가지로 수정 전후를 비교해, 달라진 결과가 모두 정확한 반올림 경계 사례임을 확인했습니다. [승인 리뷰](https://github.com/apache/fineract/pull/6731#pullrequestreview-5651881306)

[병합된 구현](https://github.com/apache/fineract/blob/6bc857bf5318c52e1aadcfb05471d0b4f4b8464e/fineract-tax/src/main/java/org/apache/fineract/portfolio/tax/service/TaxUtils.java#L39-L76) · [테스트](https://github.com/apache/fineract/blob/6bc857bf5318c52e1aadcfb05471d0b4f4b8464e/fineract-tax/src/test/java/org/apache/fineract/portfolio/tax/service/TaxUtilsTest.java) · [CI 통과](https://github.com/apache/fineract/actions/runs/40096099050)

### 함께 한 일: 불안정 테스트 보고

첫 CI에서 이 변경과 무관한 E2E 테스트 하나가 실패했습니다. 원인을 따라가 보니 전날 추가된 운전자금 대출 시나리오였습니다. 같은 시나리오가 `develop`의 한 커밋에서는 실패하고, 이 PR이 기준으로 삼은 커밋에서는 통과했습니다. 즉 불안정한 테스트였습니다. 프로젝트 기여 안내에 따라 세 번의 실행 결과를 근거로 보고하고 빌드를 다시 돌렸습니다. [FINERACT-2989](https://issues.apache.org/jira/browse/FINERACT-2989)

<sub>2026년 10월 26일 확인. 수치는 기록된 검증 범위이며, 불안정 테스트 보고는 병합 PR 수에 포함하지 않았습니다.</sub>
