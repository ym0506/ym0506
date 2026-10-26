# Apache Fineract contributions

[Profile](./README.md) · [All contributions](./CONTRIBUTIONS.md) · [한국어](./FINERACT_CONTRIBUTIONS.ko.md)

## Withholding tax off by one minor unit — money correctness

**Merged upstream** · [Issue FINERACT-2988](https://issues.apache.org/jira/browse/FINERACT-2988) → [PR #6731](https://github.com/apache/fineract/pull/6731) · October 26, 2026 (UTC)

Apache Fineract is an open source core banking platform for loans, savings and accounting. `TaxUtils.splitTax` and `splitTaxData` calculate the tax components for **withholding tax on savings interest** and for **tax on charges**. They multiplied the amount by the rate in `double` and then rounded with the configured rounding mode.

When the exact result lies on a rounding boundary, the `double` value lands just below it, so the rounding goes the wrong way and the tax is one minor unit too low:

| Amount × rate | Exact | Rounding | Expected | Before |
|---|---|---|---|---|
| 4.10 × 15 % | 0.615 | HALF_EVEN, 2 decimals | 0.62 | **0.61** |
| 5,250 × 1.4 % | 73.5 | HALF_EVEN, 0 decimals | 74 | **73** |
| 5,500 × 1.4 % | 77 | DOWN, 0 decimals | 77 | **76** |

A scan of whole amounts 1–1,000,000 at 1.4 % found **187 affected amounts under HALF_EVEN** and 374 under HALF_UP. Rates such as 10 %, 14 % and 16 % showed no differences, which is why the existing tests (e.g. 1,000 at 16 %) never caught it. The `double` calculation dates back to the original savings withholding tax feature (2016).

The fix computes `amount × rate / 100` exactly with `BigDecimal` and rounds once to the requested scale. Only results that are exactly on a rounding boundary change.

### Scope and verification

- **2 changed files:** 1 production file (+7 / −10 lines) and 1 new test class.
- **6 new regression cases** in `TaxUtilsTest`, covering both methods, HALF_EVEN ties, a zero-decimal currency, and DOWN on an exact whole amount. **5 of the 6 failed before the fix**, and all pass after it.
- Module checks passed: `fineract-tax` tests (17), formatting and style checks, and the savings withholding tax unit test.
- **Maintainer verification:** before merging, the reviewer reproduced the three cases. They also compared old and new results for amounts 0.01–2,000.00 at rates 0.5–30 % under three rounding modes and confirmed that every difference was an exact rounding-boundary case. [Approval](https://github.com/apache/fineract/pull/6731#pullrequestreview-5651881306)

[Merged implementation](https://github.com/apache/fineract/blob/6bc857bf5318c52e1aadcfb05471d0b4f4b8464e/fineract-tax/src/main/java/org/apache/fineract/portfolio/tax/service/TaxUtils.java#L39-L76) · [Tests](https://github.com/apache/fineract/blob/6bc857bf5318c52e1aadcfb05471d0b4f4b8464e/fineract-tax/src/test/java/org/apache/fineract/portfolio/tax/service/TaxUtilsTest.java) · [CI passed](https://github.com/apache/fineract/actions/runs/40096099050)

### Supporting work: flaky test report

The first CI run had one unrelated end-to-end failure. I traced it to a Working Capital loan scenario that had been added the day before. The same scenario failed on `develop` at one commit and passed at the commit this PR was based on, so the test was flaky. Following the project's contribution guide, I reported it with the three runs as evidence and re-ran the build. [FINERACT-2989](https://issues.apache.org/jira/browse/FINERACT-2989)

<sub>Verified October 26, 2026. Counts describe the recorded validation; the flaky test report is not counted as a merged PR.</sub>
