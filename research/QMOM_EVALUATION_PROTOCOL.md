# QMOM-Terminal Live Evaluation Protocol & Governance Specification
**Document ID:** SPEC-QMOM-GOV-2026-V1.0  
**Effective Date:** 2026-11-15  
**Classification:** Post-Hoc Governance Audit & Live Evaluation Standard  
**Governing Strategy:** QMOM-Terminal (NSE Large & Mid-Cap Quality + Momentum)  
**Governance Classification:** `REJECTED / UNCONFIRMED (FAILED PRE-REGISTERED HURDLES)`  

---

## 1. Configurations Evaluated & Multiple Testing Control

All parameter sweeps and sensitivity variations are registered in [config_ledger.csv](config_ledger.csv):
* **Hypothesis Search Space ($M = 41$ configurations)**:
  - Lookback pairs $(12M, 6M)$: (252, 126), (189, 95), (126, 63)
  - Volatility windows: 40, 60, 90 trading days
  - Target slot allocations: 12, 15, 18, 20 stocks
  - Friction stress tiers: 25 bps, 50 bps, 75 bps slippage per leg
  - Drift buffer: Operational band $[4.50\%, 9.50\%]$ vs unbuffered
  - Macro regime gate: 200-day EMA filter on Nifty 50 vs unconstrained
* **Multiple Testing Correction**:
  - Raw empirical permutation $p$-value: **$p = 0.010$**.
  - Bonferroni significance threshold across $M = 41$ trials:
    $$\alpha_{\text{Bonferroni}} = \frac{0.05}{41} \approx 0.00122$$
  - Adjusted Bonferroni $p$-value:
    $$p_{\text{adj}} = \min(1.0, 0.010 \times 41) = \mathbf{0.410}$$
* **Multiple Testing Verdict**: Because $p_{\text{adj}} = 0.410 > 0.05$, the strategy does **NOT** achieve statistical significance after accounting for the 41 evaluated configurations.

---

## 2. Compulsory Benchmarks & Evaluation Hurdles

To prevent strawman comparisons against weak baselines (such as the equal-weight quality pool B4 at 12.48%), any prospective forward evaluation must measure strictly against:
1. **Primary Beta Benchmark:** **B2: Nifty Midcap 150 TRI** (19.17% CAGR, -21.73% Max DD).
2. **Sister Factor Benchmark:** **B5: Forge Pure Midcap Momentum** (26.38% Net CAGR, -25.18% Max DD, Calmar = 1.05).

### Prospective Paper Evaluation Hurdles (If Run):
* **Inception Date:** None scheduled. QMOM is closed. If it is ever re-opened, forward paper trading would start on a real trading day (a Sunday `2026-11-15` seed was purged as an invalid closed-market date).
* **12-Month Review Gate (Due November 2027)**:
  1. **Data Integrity:** 100% of order fills must be verifiable on genuine exchange Open prices.
  2. **Net Excess vs Midcap Index (B2):** Annualized net excess return over B2 must be strictly $\ge +3.0$ percentage points after all transaction costs and taxes.
  3. **Risk-Adjusted vs Forge (B5):** Strategy Calmar ratio must exceed B5's Calmar ($\ge 1.05$).
  4. **Drawdown Ceiling:** Peak-to-trough drawdown strictly shallower than **`-22.0%`**.
* **24-Month Review Gate (Due November 2028)**:
  - **PASS:** Annualized net excess return of at least **`+4.0 percentage points`** over B2 with Calmar $\ge 1.10$.
  - **FAIL:** Net excess return $\le +1.5$ percentage points over B2 (insufficient to cover active trading taxes).

---

## 3. Structural Governance & Active Engine Limits

1. **Active Engine Rule:** Project CODEX strictly limits active production engines to **two**.
2. **Non-Independence:** QMOM shares over 60% portfolio overlap with Forge-Terminal's midcap momentum universe. It does not provide an independent return stream.
3. **Economic Drag:** QMOM trailed Forge-style momentum by 606 bps with a lower Calmar ratio; because QMOM differs from B5 in several ways, the isolated effect of the quality screen was not measured.
4. **Current Status:** QMOM-Terminal is **REJECTED** from active engine status and maintained solely as an archived research artifact.
