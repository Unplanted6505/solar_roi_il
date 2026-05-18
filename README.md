# Solar ROI Analyst IL 2026

A high-precision financial modeling tool designed specifically for the **Illinois residential solar market** as of June 2026. This calculator is built for retail consumers evaluating outright cash purchases of solar and battery storage systems in a "Post-ITC" and "Net Billing" environment.

## 🎯 Project Goal
Traditional solar calculators often rely on outdated 1:1 net metering assumptions and national averages. This tool provides a hyper-local analysis for Illinois homeowners, accounting for the unique utility rate structures of ComEd and Ameren and the evolving SREC (Illinois Shines) payout schedules.

---

## 🏗️ Methodology & Calculations

### 1. The Post-ITC Financial Model
As of June 2026, this model assumes the **Federal Investment Tax Credit (ITC) has expired**. Consequently, the ROI is driven entirely by:
*   Direct energy savings (Avoided Cost).
*   Illinois Shines SREC payments.
*   Utility Distributed Generation (DG) and Battery rebates.
*   Intelligent arbitrage and grid services.

### 1. Risk-Aware Modeling (Confidence Bands)
Unlike traditional calculators that provide overly deterministic point estimates, this tool employs **Confidence Bands** across all major KPIs:
*   **ROI Trajectory:** Visualizes the impact of market volatility and weather variation.
*   **Breakeven & ROI:** KPIs are displayed as a range (e.g., "7-9 Years") to account for conservative vs. aggressive arbitrage spreads and production variance.
*   **Weather Volatility:** Includes a ±5% production variance to model "bad weather" vs. "good weather" years.

### 2. Illinois Shines (SREC) Payout Structure
The tool models the **June 2026 "50/50" Payout Split**:
*   **50% Upfront:** Applied as a cash infusion in Year 0.
*   **50% Tail:** The remaining value is distributed evenly over the first 6 years of the system's life.
*   **Ownership Bonus:** Assumes a +$20/SREC premium for direct ownership vs. third-party arrangements.

### 3. Net Billing vs. Net Metering
Illinois has transitioned away from 1:1 Net Metering. The calculation engine uses a **Self-Consumption Simulation**:
*   **Self-Consumed Energy:** Valued at the **Full Retail Rate** (~$0.171/kWh for ComEd).
*   **Exported Energy:** Valued at the **Supply-Only Rate** (~$0.096/kWh for ComEd).
*   **Simulation Logic:** Monthly load/production weights are used to estimate how much solar is used in real-time vs. sent to the grid.

### 4. Battery Storage & Intelligent Arbitrage
The battery model is optimized for modern high-efficiency LFP systems and includes:
*   **Round-Trip Efficiency:** Modeled at **88%** (94% charge / 94% discharge factor), reflecting the performance of premium high-voltage hybrid systems.
*   **Annual Equivalent Full Cycles (EFC) KPI:** Provides visibility into battery wear by tracking combined throughput from solar storage and grid arbitrage.
*   **BESH Arbitrage Strategy:** For ComEd customers on Hourly Pricing, the tool models a probabilistic range of energy arbitrage income ($0.04 - $0.12/kWh spread), visualized as a shaded "cone" on the ROI trajectory. This communicates market volatility rather than presenting a single arbitrary figure.
*   **Conservative PLC Reduction:** Calculates savings from lowering the Peak Load Contribution (Capacity Charge). 
    *   *Note:* A **0.8 reliability factor** is applied to account for the difficulty in predicting Illinois's retroactive peak hours.
*   **Degradation:** Defaulting to a **1.5% annual capacity loss**, reflecting the superior durability of premium LFP chemistry.

### 5. Operating Costs & Transparency
*   **Annual Insurance:** A user-defined flat annual amount (defaulting to **$150/year**), scaling with inflation. This reflects real-world policy behavior where insurance adjustments are typically fixed riders rather than strictly proportional to system cost.
*   **Annual Maintenance:** A user-defined dollar amount to account for non-warranty labor or cleaning.
*   **Component Replacement:** Sinking funds for Inverter (Year 15) and Battery (Year 15).
*   **Hover Transparency:** Detailed yearly breakdowns (Savings, Arbitrage, SRECs, Expenses) are available by hovering over any data point on the ROI chart.

---

## 💻 Technical Implementation
*   **Single-File Web App:** HTML5/TailwindCSS/Chart.js.
*   **No Backend:** All calculations run locally in the browser.
*   **State Persistence:** Uses `localStorage` to save system comparisons.
*   **Compression:** Uses `LZ-String` to generate highly compressed shareable URLs containing full system specs.

## ⚖️ Disclaimer
This tool is for educational and analytical purposes only. Solar production is subject to weather volatility, and utility rates are subject to change by the ICC. Always verify quotes with a certified Illinois Distributed Generation Installer.

---
*Developed for the Illinois Solar Community - June 2026 Edition (v1.2.0).*
