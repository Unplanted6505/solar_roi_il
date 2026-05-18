# Technical Specification: Illinois Solar ROI Model (2026)

This document details the mathematical framework, constants, and simulation logic used in the Solar ROI Analyst tool. It is designed for technical auditing and cross-model validation.

---

## 1. Constants & Baseline Assumptions

### Utility Rate Structures (Projected June 2026)
| Utility | Full Retail Rate ($/kWh) | Supply-Only / Export Rate ($/kWh) | Monthly Fixed Charge ($) |
| :--- | :--- | :--- | :--- |
| **ComEd** | $0.171 | $0.096 | $15.00 |
| **Ameren** | $0.165 | $0.088 | $15.00 |

*   **Capacity Charge (PLC):** Estimated at $120 per PLC unit per year.
*   **Net Billing Logic:** Systems are modeled under a non-1:1 environment. Savings = `(Direct Use * Retail Rate) + (Exported * Supply Rate)`.

### Incentive Values
*   **Utility DG Rebate:** $300 per kW (Solar Size).
*   **Utility Battery Rebate:** $300 per kWh (Battery Size).
*   **SREC Base Rate (Illinois Shines):**
    *   ComEd: $78/SREC (+$20 for Direct Ownership).
    *   Ameren: $70/SREC (+$20 for Direct Ownership).
*   **SREC Payout:** 50% Upfront (Year 0), 50% Tail (spread over Years 1-6).
*   **SREC Fee:** Default 15% (Brokerage/Aggregator fee).

---

## 2. Simulation Logic

### Load & Production Distribution
The model uses monthly weights to simulate Illinois weather and seasonal usage patterns:
*   **Solar Weights:** `[0.45, 0.65, 0.90, 1.15, 1.35, 1.50, 1.50, 1.35, 1.10, 0.85, 0.55, 0.40]` (Jan-Dec).
*   **Load Weights:** `[0.85, 0.80, 0.85, 0.90, 1.00, 1.30, 1.45, 1.35, 1.10, 0.90, 0.85, 0.95]` (Jan-Dec).

### Self-Consumption Model
Daily savings are calculated via a volatility-adjusted simulation:
1.  **Direct Use Rate:** Derived from user lifestyle (Standard: 25%, WFH: 35%, etc.).
2.  **Daily Direct Use:** `min(Daily Production, Daily Load * Direct Use Rate)`.
3.  **Battery Capture:** `min(Excess Solar, Effective Battery Capacity * 0.94, Nightly Load / 0.94) * Volatility`.
4.  **Volatility Factor:** `min(0.95, 0.85 + (Battery:Solar Ratio * 0.05))`. This rewards larger storage with higher capture reliability.

### Weather & Production Volatility
To account for inter-annual weather variation, the model calculates three distinct production paths:
*   **Moderate (Expected):** 100% of P50 solar production estimate.
*   **Low (Conservative):** 95% of P50 estimate (-5% variance).
*   **High (Aggressive):** 105% of P50 estimate (+5% variance).

### Battery Efficiency
*   **Round-Trip Efficiency:** Modeled as **88%**.
*   Calculated as: `Captured Energy * 0.94` (Charge) followed by another `* 0.94` factor during discharge/offset. Reflects modern high-voltage LFP storage.

---

## 3. Financial Formulas

### Annual Expenses
*   **Insurance:** Flat annual dollar amount, inflation-adjusted. Default: **$150/year**. 
    *   *Rationale:* Homeowner insurance adjustments for solar typically involve a policy rider or adjustment that does not scale strictly linearly with system cost.
*   **Maintenance:** Flat annual fee, inflation-adjusted.
*   **Component Replacement:** Sinking fund deduction at Year 15 (Battery) and Year 15 (Inverter).

### Arbitrage & Grid Services (BESH/Hourly Pricing)
Calculated if Intelligent Arbitrage is enabled:
1.  **PLC Savings:** `min(Base PLC, Battery Size / 4) * $120 * 0.8 (Reliability Factor)`.
    *   *Audit Note:* The 0.8 factor accounts for the retroactive and predictive nature of identifying the 5 annual peak hours in the Illinois market.
2.  **Energy Arbitrage:** `Battery Size * 0.8 (DoD) * 150 (EFC/yr) * Net Spread`.
3. **Net Spread Visualization:** Modeled as a probability range on the ROI trajectory:
    *   **Conservative Boundary:** $0.04/kWh
    *   **Expected (Main Line):** $0.08/kWh
    *   **Aggressive Boundary:** $0.12/kWh
    *   *Calculation:* `Selected Spread - (0.15 * (0.02 + (Retail Rate - Export Rate)))`. (Adjusted for round-trip efficiency and delivery offset).
    *   *Comparison Note:* In multi-system comparison mode, only the Expected ($0.08) scenario is displayed for clarity.
4.  **Annual Equivalent Full Cycles (EFC) KPI:** `Total EFC = Solar EFC + max(25, 150 - (Solar EFC * 0.6))`. 
    *   *Coordinated Dispatch:* This models the "Displacement" effect where solar-charged energy already satisfies evening arbitrage windows. Grid-charging cycles are primarily used to "fill the gaps" during low-solar months. Provides a realistic throughput estimate for modern LFP systems.

### Payback & ROI (Confidence Bands)
The model rejects deterministic point estimates in favor of probabilistic ranges.
1.  **Expected Value:** Based on Moderate Arbitrage and 100% Production.
2.  **Conservative Boundary (Low ROI / High Payback):** Conservative Arbitrage + 95% Production.
3.  **Aggressive Boundary (High ROI / Low Payback):** Aggressive Arbitrage + 105% Production.
*   **Payback Period:** The first year where `Cumulative Cash Flow >= 0`. Shown as a range to communicate risk.
*   **Net Investment:** `(Solar Cost + Battery Cost + Roof Cost) - (Upfront Rebates + Upfront SRECs)`.
*   **Benchmark:** Comparison against an S&P 500 equivalent (Default 7% CAGR) using the same initial outlay.

---

## 4. Degradation Factors
*   **Solar Panels:** 0.5% annual production loss.
*   **Battery Capacity:** 1.5% annual capacity loss (impacts both arbitrage and self-consumption capture).
    *   *Rationale:* While performance guarantees (warranties) often floor at 70% at Year 10 (3%/yr), LFP (Lithium Iron Phosphate) chemistry realistically performs closer to 1.5% in residential environments, hitting ~85% at Year 10.

---
*Technical Spec Version: 1.2.0 (Confidence Band Update - June 2026)*
