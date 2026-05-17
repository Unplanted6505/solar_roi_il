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
3.  **Battery Capture:** `min(Excess Solar, Effective Battery Capacity * 0.9, Nightly Load / 0.9) * Volatility`.
4.  **Volatility Factor:** `min(0.95, 0.85 + (Battery:Solar Ratio * 0.05))`. This rewards larger storage with higher capture reliability.

### Battery Efficiency
*   **Round-Trip Efficiency:** Modeled as **81%**.
*   Calculated as: `Captured Energy * 0.9` (Charge) followed by another `* 0.9` factor during discharge/offset.

---

## 3. Financial Formulas

### Annual Expenses
*   **Insurance:** `Solar Array Cost * Insurance Rate (%)`. Default: 0.75%.
*   **Maintenance:** Flat annual fee, inflation-adjusted.
*   **Component Replacement:** Sinking fund deduction at Year 12 (Battery) and Year 15 (Inverter).

### Arbitrage & Grid Services (BESH/Hourly Pricing)
Calculated if Intelligent Arbitrage is enabled:
1.  **PLC Savings:** `min(Base PLC, Battery Size / 4) * $120`. Assumes the battery can shed 4 hours of peak load during capacity calls.
2.  **Energy Arbitrage:** `Battery Size * 0.8 (DoD) * 150 (Cycles/yr) * Net Spread`.
3.  **Net Spread:** `Arbitrage Spread - (0.15 * (0.02 + Delivery Rate))`. This accounts for efficiency losses and delivery-side friction.

### Payback & ROI
*   **Payback Period:** The first year where `Cumulative Cash Flow >= 0`.
*   **Net Investment:** `(Solar Cost + Battery Cost + Roof Cost) - (Upfront Rebates + Upfront SRECs)`.
*   **Benchmark:** Comparison against an S&P 500 equivalent (Default 7% CAGR) using the same initial outlay.

---

## 4. Degradation Factors
*   **Solar Panels:** 0.5% annual production loss.
*   **Battery Capacity:** 2.5% annual capacity loss (impacts both arbitrage and self-consumption capture).

---
*Technical Spec Version: 1.0.0 (June 2026 IL Market Profile)*
