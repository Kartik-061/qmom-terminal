# QMOM-TERMINAL: POST-HOC EMPIRICAL EVALUATION & METHODOLOGICAL DEFECT AUDIT
**Document ID:** SPEC-QMOM-2026-V1.0  
**Version:** v2.0_POST_HOC_AUDITED_EVALUATION  
**Effective Date:** 2026-11-15  
**Project:** Project CODEX Multi-Asset Quantitative House  
**Market:** National Stock Exchange of India (NSE)  
**Asset Class:** Liquid Indian Large & Mid-Cap Equities  
**Sister Engine:** Forge-Terminal (NSE Midcap Momentum: 27.08% Net CAGR, -20.47% Max DD)  
**Governance Classification:** `REJECTED / UNCONFIRMED (FAILED PRE-REGISTERED HURDLES)`  

---

## 1. Executive Summary & Core Hypothesis

### 1.1 The Vulnerability of Pure Momentum
Pure cross-sectional relative price momentum (Jegadeesh & Titman 1993) capitalizes on market underreaction and multi-month capital commitment. However, in late-stage equity cycles, highly leveraged, cyclical, and low-margin enterprises frequently stage explosive speculative rallies. When macroeconomic liquidity contracts, these low-quality momentum leaders suffer sharp, non-linear drawdowns (Daniel and Moskowitz 2016).

### 1.2 The Quality Factor Shield Hypothesis
The QMOM hypothesis posited that combining fundamental quality screens (Asness, Frazzini, and Pedersen 2019; Novy-Marx 2013) with momentum ranking would filter out fragile balance sheets *ex-ante*, cutting drawdowns to $\le -16.0\%$ while preserving $20.0\% \text{ to } 25.0\%+$ net compounding.

### 1.3 Empirical Reality: QMOM Underperformed Plain Momentum (Quality Not Isolated)
Empirical testing on genuine NSE exchange sessions (**1,235 trading days**, `2021-09-20` to `2026-09-18`, active trading from `2022-11-01` to `2026-09-18`) disproves this thesis:
1. **Forge Pure Momentum (B5):** **26.38% Net CAGR** (and 32.73% active), **-25.18% Max DD**, **Calmar = 1.05**.
2. **QMOM Core Strategy:** **20.32% Net CAGR** (and 24.25% active), **-21.00% Max DD**, **Calmar = 0.97**.
3. **The Gap to Forge-Style Momentum:** QMOM trailed B5 by **606 bps a year** while compressing drawdown by only **4.18 percentage points**, and its Calmar ratio fell from 1.05 to 0.97. QMOM also differs from B5 in position count, regime gate, skip-month, drift buffer and momentum floor, so the isolated effect of quality was not measured.
4. **Weak Edge Over Plain Midcap Beta:** On the full window QMOM (20.32%) barely edges past the passive Nifty Midcap 150 index fund B2 (19.17%, Max DD -21.73%); on the active window the edge is +401 bps, but out-of-sample QMOM returned -1.11%. Taxes were not modeled.

---

## 2. Pre-Registered Hurdles vs. Empirical Findings

Under the original hurdles in `SPEC-QMOM-2026-V1.0` (which were calibrated to the first, invalid run and are not true pre-registration), QMOM-Terminal **FAILED** its primary risk and performance hurdles:

| Metric / Gate | Original Pre-Registered Hurdle | Empirical Backtest Result | Audit Verdict |
| :--- | :---: | :---: | :---: |
| **Max Drawdown Ceiling** | Strictly $\le -16.0\%$ | **-21.00%** | **FAIL (Breached by 500 bps)** |
| **Gate 1: Next-Bar Open Fills** | 100% verified on genuine Open | **Only 8/77 symbols have Open (10.4%)** | **FAIL (Unverifiable Data)** |
| **Gate 2: 2x Cost Stress (50 bps)** | Net CAGR $\ge 20.0\%$ | **19.63%** | **FAIL (Missed by 37 bps)** |
| **Gate 3: Andrew Lo 95% LCB** | Lower Confidence Bound $> 0.40$ | **0.13** (Lo SE: 0.47) | **FAIL (Below threshold)** |
| **Gate 4: Out-of-Sample CAGR** | OOS Net CAGR $\ge 18.0\%$ | **-1.11%** (Quality OOS: -6.90%) | **FAIL (Absolute target missed)** |
| **Gate 6: Multiple Testing ($p_{\text{adj}}$)** | $p_{\text{adj}} < 0.05$ across 41 runs | **$p_{\text{raw}} = 0.010, p_{\text{adj}} = 0.410$** | **FAIL (Bonferroni insignificance)** |
| **Strategy Governance Status** | 9 / 9 Gates Passing | **6 / 9 Gates Failed** | **REJECTED** |

---

## 3. Methodological & Data Audit

### 3.1 Gate 1: The Missing Open Column & Fabricated Volume
* **Defect:** In `Forge-Terminal/data/midcap150_daily`, only 16 of 233 files possess an `Open` column. Within the 77 quality candidates, **only 8 symbols (10.4%)** have genuine exchange Open prices.
* **Impact:** For 69 symbols, `o = c.shift(1)` was used, meaning the order executed at decision-bar Close $P_{t}$. The claim of "186/186 fills verified at Next-Bar Open" was false.
* **Volume Fabrication:** Missing volume was filled with `fillna(100000)`. This artificial padding was removed; genuine volume is preserved with missing values set to NaN.

### 3.2 Warmup Window & Misleading 2022 Drawdown
* **Defect:** Historical data starts on `2021-09-20`. Because the strategy requires a 252-day lookback, active trading only commenced on **`2022-11-01`**.
* **Impact:** For the first 13.5 months, the strategy held 100% cash. The reported "2022 Rate Hikes Drawdown: 0.0%" was entirely an artifact of being uninvested during warmup, not macro regime protection.
* **Effective Sample:** Active trading spans only **3.88 years** (47 months, `2022-11-01` to `2026-09-18`).

### 3.3 Reconstitution Timeline Mismatch
* **Defect:** `data/universe.py` contains 20 hard-coded reconstitution events dated in March and September.
* **Impact:** An earlier audit note claimed NSE reconstitutes these indices in **June and December**; that claim was not checked against NSE methodology documents and may be wrong. No NSE source files are shown for the 20 events. Events prior to 2018/2019 pre-date live index operations.

### 3.4 Seed Date Correction
* **Defect:** Initial seed record was dated Sunday, `2026-11-15` (market closed).
* **Resolution:** Purged Sunday seed. No forward paper trading is scheduled; QMOM is closed.

---

## 4. Compulsory Baselines Comparison

Simulated across the active trading window (`2022-11-01` to `2026-09-18`, 3.88 years) and full 5-year data window:

| Strategy / Baseline | Active CAGR | Full CAGR | Max Drawdown | Annual Volatility | Calmar |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **QMOM-Terminal Core** | **24.25%** | **20.32%** | **-21.00%** | **12.92%** | **0.97** |
| **B1: Nifty 50 (Price Index)** | 6.71% | 6.19% | -17.23% | 13.91% | 0.36 |
| **B2: Nifty Midcap 150 TRI** | 20.24% | 19.17% | -21.73% | 18.45% | 0.88 |
| **B3: Passive Quality 50** | 12.21% | 12.44% | -25.50% | 16.39% | 0.49 |
| **B4: Equal-Weighted Quality** | 12.23% | 12.48% | -25.47% | 16.42% | 0.49 |
| **B5: Forge Pure Momentum** | **32.73%** | **26.38%** | **-25.18%** | **19.86%** | **1.05** |

*Note on B1:* Nifty 50 is loaded from unadjusted closing prices (Price Return Index, PRI). With dividend reinvestment (~1.3% p.a.), actual Nifty 50 TRI compounded at ~7.5%.  
*Note on B5:* Baseline 5 is a simplified Forge-style reimplementation in QMOM's harness, differing from official Forge-Terminal (27.08% Net CAGR, -20.47% Max DD).

---

## 5. Governance Decision: Strategy Demotion & Closure

Under Project CODEX Multi-Asset Quantitative House governance:
1. **Active Engine Limit:** CODEX strictly permits a maximum of **two active engines**.
2. **Portfolio Overlap:** QMOM shares 60%+ constituent overlap with Forge-Terminal's midcap momentum universe.
3. **Negative Result (Quality Not Isolated):** QMOM trailed Forge-style momentum by 606 to 848 bps and its Calmar ratio was 0.97 against 1.05.
4. **Weak Edge:** QMOM does not reliably beat passive midcap beta (B2): +115 bps on the full window and -1.11% out-of-sample.
5. **Verdict:** **QMOM-Terminal is REJECTED as an active engine.** Research code, audit findings, and negative results are archived for research provenance.
