# QMOM-Terminal (Quality + Momentum Equity Engine)
**Project CODEX Multi-Asset Quantitative House**  
**Market:** National Stock Exchange of India (NSE)  
**Asset Class:** Liquid Indian Large & Mid-Cap Equities  
**Sister Engine:** Forge-Terminal (NSE Midcap Momentum: 27.08% Net CAGR, -20.47% Max DD)  
**Research Audit Memo:** [research/QMOM_RESEARCH_SPECIFICATION.md](research/QMOM_RESEARCH_SPECIFICATION.md) (`SPEC-QMOM-2026-V1.0`)  
**Evaluation Protocol:** [research/QMOM_EVALUATION_PROTOCOL.md](research/QMOM_EVALUATION_PROTOCOL.md)  
**Governance Classification:** `REJECTED / UNCONFIRMED (FAILED PRE-REGISTERED HURDLES)`  
**Forward Inception Status:** None. Closed as an active engine; no forward paper trading is scheduled.  

---

## 1. Executive Summary & Core Empirical Findings

QMOM-Terminal was developed to test whether marrying an official point-in-time **Quality screen** (Asness, Frazzini, and Pedersen 2019; Novy-Marx 2013) with **Dual-Horizon Relative Momentum** could compress peak-to-trough drawdowns to $\le -16.0\%$ while maintaining $20\% \text{ to } 25\%+$ net compounding in Indian equities.

A rigorous audit across **1,235 genuine NSE trading sessions** (`2021-09-20` to `2026-09-18`, with active trading from `2022-11-01` to `2026-09-18`) revealed three decisive empirical and methodological findings:

### 1. QMOM Underperformed Forge-Style Momentum (the Quality Filter's Isolated Effect Was Not Measured):
* **Forge-Style Pure Midcap Momentum (B5):** **26.38% Net CAGR** (and 32.73% active), **-25.18% Max DD**, **Calmar = 1.05**.
* **QMOM-Terminal Core Strategy:** **20.32% Net CAGR** (and 24.25% active), **-21.00% Max DD**, **Calmar = 0.97**.
* **Finding:** QMOM trailed Forge-style momentum by **6.06 percentage points (-606 bps)** while compressing drawdown by only **4.18 percentage points**, and its Calmar ratio fell from 1.05 to 0.97. **Caveat:** QMOM also differs from B5 in position count (15 vs 30), the 200-day regime gate, the skip-month, the drift buffer and an absolute momentum floor, so the isolated effect of the quality filter was not measured (in Forge, the 200-day gate alone cost about 2.4 points of CAGR). The idea that quality names lag midcap rallies is a hypothesis, not a result.

### 2. Weak Edge Over a Plain Midcap Index Fund, and a Failed Out-of-Sample Test:
* **B2: Nifty Midcap 150 Index Fund:** **19.17% CAGR** (and 20.24% active), **-21.73% Max DD**.
* **QMOM-Terminal:** **20.32% Net CAGR** (and 24.25% active), **-21.00% Max DD**.
* **Finding:** Over the full 5-year window (which includes 13.5 months of warm-up cash for QMOM) the edge is only **+1.15% (115 bps)**. Over the active window it is 24.25% against 20.24% (+401 bps), with similar maximum drawdowns (-21.00% vs -21.73%). That in-sample edge did not survive out-of-sample: the walk-forward test shows in-sample 37.06% against out-of-sample -1.11%. A tax model was not run, but 20% STCG on monthly rotation would shrink any remaining edge.

### 3. Failure of Pre-Registered Research Hurdles:
* Under the original hurdles in `SPEC-QMOM-2026-V1.0`, QMOM **FAILED 6 of 9 robustness gates** (Gates 1, 2, 3, 4, 6 and 7: Max DD breached the -16.0% ceiling; 2x cost missed the 20.0% hurdle; OOS CAGR missed the 18.0% hurdle; the Andrew Lo 95% LCB missed 0.40; Gate 1 failed on missing Open prices; Gate 6 failed after Bonferroni).
* The hurdles were not truly pre-registered: the first set was calibrated to the first (invalid, synthetic-data) run and then loosened after the real-data run, which is curve-fitting. The rejection therefore rests on the baseline comparisons, the out-of-sample result (-1.11%) and the Bonferroni test, not on those hurdles. The strategy is formally **REJECTED**.

---

## 2. Exhaustive Methodological & Data Audit

| # | Severity | Methodological Defect | Directional Bias | Econometric & Engineering Reality |
| :---: | :---: | :--- | :---: | :--- |
| **1** | **CRITICAL** | **Gate 1 Execution Falsehood (Missing Open Prices)** | **Unverifiable Fills** | In `Forge-Terminal/data/midcap150_daily`, only 16 of 233 files possess an `Open` column. Within the 77 quality symbols, **only 8 symbols (10.4%)** have Open data. For the remaining 69 symbols, `o = c.shift(1)` was used, meaning orders executed at decision-bar Close $P_{t}$. "186/186 fills verified at Next-Bar Open" was false. Gate 1 **FAILS**. |
| **2** | **CRITICAL** | **Volume Fabrication via `fillna(100000)`** | **Concealing Illiquidity** | `universe.py` line 373 filled missing volume with 100,000 shares. This fabricated liquidity for days with missing exchange volume. **Purged:** Missing volume is preserved as NaN/0.0. |
| **3** | **CRITICAL** | **Pre-Registration Hurdle Hugging (HARKing)** | **Post-Hoc Fitting** | Hurdles were softened after seeing backtest outputs (e.g. Drawdown ceiling shifted from -16.0% to -22.0% after observing -21.00%; 2x cost shifted from 20% to 18% after observing 19.63%). Under original pre-registered hurdles, QMOM **FAILED**. |
| **4** | **HIGH** | **Warmup Window Artifact (Misleading 2022 DD)** | **Artificially Zero Drawdown** | Data begins on `2021-09-20`. The 252-day lookback delayed the first trade until **`2022-11-01`**. For the first 13.5 months, the portfolio held 100% cash. The reported "2022 Rate Hikes: 0.0% drawdown" was an artifact of being in cash during warmup, not regime protection. |
| **5** | **HIGH** | **Quality Factor Performance Drag** | **Negative Value-Add** | Adding Quality to Momentum reduced CAGR by 606 bps (26.38% -> 20.32%) while only trimming drawdown by 418 bps, degrading the Calmar ratio from 1.05 to 0.97. |
| **6** | **HIGH** | **Gate 6 Permutation Test Resolution Floor** | **Masking Insignificance** | 100 permutations floor the $p$-value at $1/100 = 0.010$. After testing 41 configurations in `config_ledger.csv`, the Bonferroni threshold is $\alpha / 41 = 0.00122$. The Bonferroni-adjusted $p$-value is **$p_{\text{adj}} = \min(1.0, 0.010 \times 41) = \mathbf{0.410}$**, which fails statistical significance ($p > 0.05$). |
| **7** | **MEDIUM** | **Reconstitution Timeline Discrepancy** | **Methodological Mismatch** | `universe.py` contains 20 hard-coded events (not 21) set in March and September, and no NSE source files are shown. An earlier audit note said NSE specifies **June and December** reviews for these indices; that was not checked against NSE methodology documents and may be wrong. Early events pre-date live index launches. |
| **8** | **MEDIUM** | **Closed-Market Inception Date (Sunday Nov 15)** | **Invalid Execution Date** | The draft seed record was dated Sunday, `2026-11-15` when markets are closed. **Resolved:** Purged Sunday seed; no forward paper trading is scheduled; QMOM is closed. |
| **9** | **MEDIUM** | **Benchmark B1 PRI vs TRI Understatement** | **Understating Large-Cap Beta** | Baseline B1 (Nifty 50 at 6.19%) is the Price Return Index (closing 17,396.90 on 2021-09-20 and 23,346.40 on 2026-09-18). Accounting for ~1.3% annual dividend yield, actual Nifty 50 TRI compounded at ~7.5%. |
| **10** | **LOW** | **Baseline 5 Reimplementation Discrepancy** | **Discrepancy vs Official Forge** | Baseline 5 in QMOM is a simplified Forge-style reimplementation (26.38% CAGR, -25.18% Max DD) without Forge's standalone daily order book. Official Forge-Terminal achieves 27.08% Net CAGR, -20.47% Max DD. |

---

## 3. Compulsory Baselines Comparison

Simulated across the full 5-year data window (`2021-09-20` to `2026-09-18`, 1,235 sessions) and the active trading window (`2022-11-01` to `2026-09-18`, 3.88 years, 974 sessions):

| Strategy / Baseline | Active Net CAGR | Full Net CAGR | Max Drawdown | Annual Volatility | Calmar Ratio | Status / Assessment |
| :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **QMOM-Terminal Core** | **24.25%** | **20.32%** | **-21.00%** | **12.92%** | **0.97** | **Failed Pre-Reg Hurdles** |
| **B1: Nifty 50 (Price Index)** | 6.71% | 6.19% | -17.23% | 13.91% | 0.36 | Outperformed (+1,413 bps) |
| **B2: Nifty Midcap 150 TRI** | 20.24% | 19.17% | -21.73% | 18.45% | 0.88 | +115 bps full-sample (Erased by tax) |
| **B3: Passive Quality 50** | 12.21% | 12.44% | -25.50% | 16.39% | 0.49 | Outperformed (+788 bps, -450 bps DD) |
| **B4: Equal-Weighted Quality** | 12.23% | 12.48% | -25.47% | 16.42% | 0.49 | Outperformed (+784 bps, -447 bps DD) |
| **B5: Forge Pure Momentum** | **32.73%** | **26.38%** | **-25.18%** | **19.86%** | **1.05** | **Beats QMOM by 606 to 848 bps** |

---

## 4. 9-Gate Robustness Battery Results

Audited under the original pre-registered specification (`SPEC-QMOM-2026-V1.0`):

| Gate | Validation Focus | Pre-Registered Hurdle | Observed Metric | Verdict |
| :---: | :--- | :--- | :--- | :---: |
| **Gate 0** | Data Provenance & PIT Mapping | 100% PIT constituent mapping | 20 hard-coded reconstitutions; NSE source files not shown | **UNVERIFIED** |
| **Gate 1** | Next-Bar Open Execution | 100% order fills at genuine Open | Only 8/77 symbols have Open (10.4%); 160/186 fills used Close fallback | **FAIL** |
| **Gate 2** | Cost Stress Ladder | 2x slip (50 bps) CAGR $\ge 20\%$ | Base: 20.32%, 2x: 19.63% (Missed 20%), 3x: 18.98% | **FAIL** |
| **Gate 3** | Sharpe & Andrew Lo (2002) SE | Sharpe $\ge 1.00$; Lo 95% LCB $> 0.40$ | Sharpe = 1.05, Lo SE = 0.47, Lo 95% LCB = 0.13 (Missed 0.40) | **FAIL** |
| **Gate 4** | Walk-Forward Train/Test Split | OOS Net CAGR $\ge 18.0\%$ | IS CAGR = 37.06%, OOS CAGR = -1.11% (Quality OOS: -6.90%) | **FAIL** |
| **Gate 5** | Parameter Grid & Sensitivity | 36-cell grid; $\ge 80\%$ beat Quality B3 | 31/36 beat Quality B3 (86.1%); Mean Excess = +4.07% | **PASS** |
| **Gate 6** | Permutation Luck Disproof | Empirical $p_{\text{adj}} < 0.05$ vs 100 paths | Raw $p = 0.010$, Bonferroni adjusted $p_{\text{adj}} = 0.410 > 0.05$ | **FAIL** |
| **Gate 7** | Regime Consistency | Drawdown ceiling strictly $\le \mathbf{-16.0\%}$ | Max DD = -21.00% (Breached -16.0% ceiling by 500 bps) | **FAIL** |
| **Gate 8** | Turnover & Capacity | Annual turnover $< 200\%$; Capacity $\ge ₹25\text{ Cr}$ | Turnover = 116.04%, Estimated Capacity = ₹30.76 Crores | **PASS** |

* **Pre-Registered Hard Reject Hurdles Passed:** **NO (4 Failed)**
* **Diagnostic Hurdles Passed:** **NO (Gate 6 Failed)**
* **Overall Research Verdict:** **REJECTED**

---

## 5. Governance & Active Engine Limits

1. **Active Engine Rule:** Project CODEX maintains a hard ceiling of **at most two active quantitative engines**.
2. **Redundancy with Forge-Terminal:** QMOM-Terminal exhibits over 60% portfolio name overlap with Forge-Terminal. It is not an independent return stream.
3. **Negative Result (Quality Filter Not Isolated):** QMOM underperformed Forge-style momentum in Indian midcap equities:
   - Annual Net CAGR fell by **6.06 percentage points** (26.38% -> 20.32%).
   - Calmar ratio dropped from **1.05** to **0.97**.
   - Full-window edge over a passive midcap index fund is only **115 bps** (401 bps on the active window, but -1.11% out-of-sample); taxes were not modeled.
4. **Final Disposition:** **QMOM-Terminal is closed as an active engine candidate.** Code, audit ledgers, and negative empirical results are archived in Project CODEX for research provenance.
