# Walmart Inc. (WMT) – Technical Specification

**Created by:** [Student Name]
**Date Created:** April 2026
**Version:** 1.0
**LLM Used:** Claude (Anthropic) — drafting assistance

**Role:** Financial Analyst / FP&A Analyst
**Audience:** CFO / Director of FP&A

---

## 1. Problem Statement

Walmart Inc. (NYSE: WMT) is the world's largest publicly traded general merchandise retailer. This specification documents the analytical framework for computing 27 accounting and performance ratios from Walmart's FY2025 financial statements (fiscal year ended January 31, 2025), using the FY2024 Balance Sheet as the start-of-year denominator throughout.

The model is designed to support a CFO briefing on Walmart's financial health, operational efficiency, capital structure, and value creation relative to cost of capital. Outputs feed directly into the Stage 4 AI prompt and executive interpretive memo.

---

## 2. Inputs (Known Variables)

> All figures in millions of USD. Source: Walmart 10-K FY2025, SEC EDGAR.

### Balance Sheet Items

| Variable | Description | Named Range | FY2025 | FY2024 |
|---|---|---|---|---|
| Cash & marketable securities | Liquid assets on hand | `BAL_cash_marketable_securities_[year]` | 9,037 | 9,867 |
| Receivables | Accounts receivable | `BAL_receivables_[year]` | 9,975 | 8,796 |
| Inventories | Inventory balance | `BAL_inventories_[year]` | 56,435 | 54,892 |
| Total current assets | Sum of current assets | `BAL_assets_current_[year]` | 79,458 | 76,877 |
| Net tangible fixed assets | PP&E net of depreciation | `BAL_fixed_assets_net_[year]` | 119,993 | 110,810 |
| Total assets | All assets | `BAL_assets_total_[year]` | 260,823 | 252,399 |
| Total current liabilities | Short-term obligations | `BAL_liabilities_current_[year]` | 96,584 | 92,415 |
| Long-term debt | Non-current borrowings | `BAL_debt_long_term_[year]` | 33,401 | 36,132 |
| Total liabilities | All liabilities | `BAL_liabilities_total_[year]` | 163,402 | 162,050 |
| Shareholders' equity | Book value of equity | `BAL_equity_shareholders_[year]` | 97,421 | 90,349 |

### Income Statement Items

| Variable | Description | Named Range | FY2025 |
|---|---|---|---|
| Net sales | Total revenue | `INC_sales` | 674,538 |
| Cost of goods sold | Direct product costs | `INC_cost_goods_sold` | 511,753 |
| SG&A expenses | Operating overhead | `INC_sga` | 139,884 |
| Depreciation | Non-cash expense | `INC_depreciation` | 12,973 |
| EBIT | Operating income | `INC_ebit` | 29,348 |
| Membership & other income | Non-operating income | `INC_other_income` | 6,447 |
| Interest expense, net | Net cost of debt | `INC_interest_net` | 2,245 |
| Taxes | Income tax expense | `INC_taxes` | 6,152 |
| Net income | Bottom-line profit | `INC_net` | 20,951 |
| Dividends | Shareholder distributions | `INC_dividends` | 6,688 |

### Cash Flow Statement Items

| Variable | Description | Named Range | FY2025 |
|---|---|---|---|
| Cash from operations | Operating cash flow | `CASH_operating` | 35,256 |
| Cash from investments | Investing cash flow | `CASH_investments` | (21,379) |

### Market / Analyst Inputs

| Variable | Description | Named Range | Value |
|---|---|---|---|
| Share price | Jan 31, 2025 closing price | `share_price` | $95.08 |
| Shares outstanding | Total shares in millions | `shares_outstanding` | 8,017 |
| Cost of capital | Estimated WACC | `cost_capital` | 6.5% |
| Tax rate | Analyst assumption | `tax_rate` | 21.5% |

---

## 3. Assumptions & Constraints

- All figures are in millions of USD unless otherwise noted.
- Tax rate fixed at 21.5% (analyst assumption; effective rate per 10-K is approximately 22.7% — statutory rate used for comparability).
- Cost of capital set at 6.5%, estimated as WACC; may be refined using CAPM methodology.
- Share price of $95.08 reflects the January 31, 2025 fiscal year-end closing price.
- Start-of-year denominators use the FY2024 Balance Sheet throughout all flow-to-stock ratios.
- Interest expense is reported net of interest income; `INC_interest_net` ($2,245M) is used throughout.
- Depreciation sourced from the Income Statement ($12,973M); consistent with the Cash Flow Statement add-back.
- No off-balance-sheet items, operating lease adjustments, or contingent liabilities are incorporated.
- Market capitalization computed as `share_price × shares_outstanding`; no diluted share adjustment applied.

---

## 4. Calculation Flow

### Step 1: Derived Inputs

```
market_capitalization             = share_price × shares_outstanding                              → 762,256
currentYear_after_tax_op_income   = INC_net + (1 − tax_rate) × INC_interest_net                  → 22,713
currentYear_daily_sales_average   = INC_sales / 365                                               → 1,848
currentYear_cost_goods_sold_daily = INC_cost_goods_sold / 365                                     → 1,402
currentYear_working_capital_net   = BAL_assets_current_2025 − BAL_liabilities_current_2025        → (17,126)
currentYear_total_capitalization  = BAL_debt_long_term_2025 + BAL_equity_shareholders_2025        → 130,822
startYear_total_capitalization    = BAL_debt_long_term_2024 + BAL_equity_shareholders_2024        → 126,481
avg_equity                        = AVERAGE(BAL_equity_shareholders_2024, BAL_equity_shareholders_2025) → 93,885
avg_total_assets                  = AVERAGE(BAL_assets_total_2024, BAL_assets_total_2025)         → 256,611
avg_total_capitalization          = AVERAGE(startYear_total_cap, currentYear_total_cap)           → 128,652
```

### Step 2: Performance Ratios

```
MVA            = market_capitalization − currentYear_equity                                        → 664,835
Market-to-Book = market_capitalization / currentYear_equity                                        → 7.82×
EVA            = currentYear_after_tax_op_income − (cost_capital × startYear_total_capitalization) → 14,492
```

### Step 3: Profitability Ratios

```
ROA         = currentYear_after_tax_op_income / startYear_total_assets       → 9.00%
ROC         = currentYear_after_tax_op_income / startYear_total_capitalization → 17.96%
ROE         = INC_net / startYear_equity                                      → 23.19%
ROA [avg]   = currentYear_after_tax_op_income / avg_total_assets              → 8.85%
ROC [avg]   = currentYear_after_tax_op_income / avg_total_capitalization      → 17.65%
ROE [avg]   = INC_net / avg_equity                                            → 22.32%
```

### Step 4: Efficiency Ratios

```
Asset Turnover          = INC_sales / startYear_total_assets                  → 2.673×
Receivables Turnover    = INC_sales / startYear_receivables                   → 76.69×
Avg Collection Period   = startYear_receivables / daily_sales_average         → 4.76 days
Inventory Turnover      = INC_cost_goods_sold / startYear_inventory           → 9.32×
Days in Inventory       = startYear_inventory / daily_cost_goods_sold         → 39.15 days
Profit Margin           = INC_net / INC_sales                                 → 3.11%
Operating Profit Margin = currentYear_after_tax_op_income / INC_sales         → 3.37%
```

### Step 5: Leverage Ratios

```
Long-Term Debt Ratio    = BAL_debt_long_term_2025 / (LTD + equity)           → 25.53%
Long-Term Debt-Equity   = BAL_debt_long_term_2025 / currentYear_equity        → 34.29%
Total Debt Ratio        = BAL_liabilities_total_2025 / BAL_assets_total_2025  → 62.65%
Times Interest Earned   = INC_ebit / INC_interest_net                         → 13.07×
Cash Coverage Ratio     = (INC_ebit + INC_depreciation) / INC_interest_net    → 18.85×
Debt Burden             = INC_net / currentYear_after_tax_op_income            → 0.922
Leverage Ratio          = BAL_assets_total_2025 / currentYear_equity           → 2.677×
```

### Step 6: Liquidity Ratios

```
NWC-to-Assets = currentYear_working_capital_net / BAL_assets_total_2025       → (6.57%)
Current Ratio = BAL_assets_current_2025 / BAL_liabilities_current_2025        → 0.823×
Quick Ratio   = (BAL_cash_mkt_sec_2025 + BAL_receivables_2025) / current_liab → 0.197×
Cash Ratio    = BAL_cash_marketable_securities_2025 / BAL_liabilities_current  → 0.094×
```

### Step 7: Du Pont Decomposition

```
Du Pont ROA = asset_turnover × operating_profit_margin
            = 2.673× × 3.37%                                                   → 9.00%

Du Pont ROE = leverage × asset_turnover × operating_profit_margin × debt_burden
            = 2.677× × 2.673× × 3.37% × 0.922                                 → 22.22%
```

---

## 5. Outputs

| Output | Description | Format | Purpose |
|---|---|---|---|
| Ratio summary table | All 27 ratios organized by category with computed values | Table | Core analytical output |
| Derived inputs block | Intermediate calculations (ATOI, averages, daily figures) | Table | Audit trail |
| Du Pont decomposition | Four-factor ROE and two-factor ROA breakdown | Table | Return driver analysis |
| Named-range formula column | Pseudocode formula for every output cell (Column D) | Column | Reproducibility & audit |

---

## 6. Model Review — What Worked & What to Improve

**What worked well:**
- Named-range architecture made all formulas self-documenting and eliminated ambiguous cell references — the model can be read without opening Excel.
- Du Pont ROA (9.00%) reconciled exactly with direct ROA; four-factor Du Pont ROE (22.22%) came within 100bps of direct ROE (23.19%), confirming formula consistency across sections.
- Applying the start-of-year FY2024 denominator uniformly across all flow-to-stock ratios improved comparability and avoided double-counting current-year changes.

**What to improve:**
- Net vs. gross interest: using `INC_interest_net` understates Times Interest Earned and Cash Coverage relative to standard practice. A refined model separates interest income and gross interest expense into distinct named ranges.
- Receivables in the quick ratio uses FY2025 end-of-year balances rather than start-of-year, introducing a minor timing inconsistency that should be corrected for uniformity.
- Walmart's negative NWC (−$17,126M) and current ratio below 1.0 should be annotated as a structural feature of its supplier-financing model, not flagged as a liquidity risk — an important nuance for executive audiences.
- No peer benchmarks or trend data are included. Adding Target and Costco reference columns and a FY2023–FY2025 time series would materially strengthen the analysis.

---

## 7. Limitations & Next Steps

This model excludes industry peer comparisons, multi-year trend analysis, operating lease adjustments under ASC 842, and non-GAAP figures. EVA is sensitive to the 6.5% WACC assumption and would benefit from a CAPM-derived estimate using current beta and risk-free rate. The statutory tax rate (21.5%) modestly overstates after-tax operating income relative to the effective rate (22.7%).

The next phase (Stage 4) uses this specification as a structured AI prompt blueprint — the named-range pseudocode in Section 4 maps directly to prompt parameters, and the Model Review observations define the target improvements the rebuilt model should address. The final deliverable will be an executive memo interpreting ratio results and recommending strategic actions to the CFO on capital efficiency, liquidity management, and value creation.
