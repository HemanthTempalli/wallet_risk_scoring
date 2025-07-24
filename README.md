# 📊 Compound Wallet Risk Scoring System

This project evaluates wallet-level risk scores (ranging from **0 to 1000**) based purely on **on-chain behavior** from the **Compound V2/V3 protocols**. The objective is to create a **transparent**, **scalable**, and **explainable** risk assessment framework that can be extended across various DeFi ecosystems.

---

## 📥 Data Collection Method

We used the **Covalent API** to extract historical blockchain data for 103 Ethereum wallets, focusing on interactions with Compound smart contracts. Specifically, the following event types were parsed:

- `Mint` (Supply)
- `Borrow`
- `RepayBorrow`
- `Redeem` (Withdraw)
- `LiquidateBorrow`

To augment these events, synthetic metrics were computed using token metadata and price approximations, including:

- Estimated **health factor**
- **Collateralization ratio**
- **Asset diversity** (number of unique tokens used)
- **Activity timelines** and **transaction frequency**

Wallets with no Compound activity were retained to ensure model resilience, receiving a default low-risk baseline.

---

## 🧠 Feature Selection Rationale

Features were selected through a combination of domain expertise, risk modeling best practices, and exploratory data analysis. The goal was to ensure interpretability and comprehensive coverage of wallet behaviors. Key features include:

- **Leverage Ratio** (borrowed/supplied value)
- **Liquidation Count** (number of times wallet was liquidated)
- **Utilization Ratio** (borrowed/supplied volume)
- **Volatility Score** (variance in transaction volume)
- **Asset Concentration** (degree of token diversification)
- **Activity Profile** (active days and tx count)
- **Size Risk** (flag for unusually large positions)

All features were normalized to allow fair weighting in risk computation.

---

## 📊 Scoring Method

The scoring algorithm is based on a **weighted linear aggregation** of seven risk dimensions:

| Risk Factor         | Weight |
|---------------------|--------|
| Leverage Risk       | 25%    |
| Liquidation Risk    | 20%    |
| Utilization Risk    | 15%    |
| Volatility Risk     | 15%    |
| Concentration Risk  | 10%    |
| Activity Risk       | 10%    |
| Size Risk           | 5%     |

The risk score for each wallet is computed as:

Final Score = 1000 - Σ(weighted_risk_scores)


Scores are interpreted as:

- **High Risk**: Score < 400  
- **Medium Risk**: 400 ≤ Score < 700  
- **Low Risk**: Score ≥ 700

Due to limited Compound activity, most wallets were categorized as **Low Risk**.

---

## 🚩 Justification of Risk Indicators

Each risk factor was included based on its relevance to financial health and protocol stability:

- **Leverage Risk**: Over-leveraged positions can easily become insolvent.
- **Liquidation Risk**: Past liquidations are strong predictors of poor collateral management.
- **Utilization Risk**: High utilization signals aggressive strategies.
- **Volatility Risk**: Irregular volume may suggest bot-like or unstable behavior.
- **Concentration Risk**: Diversification reduces systemic exposure.
- **Activity Risk**: Highly inconsistent or dormant wallets may indicate manipulation or exit risks.
- **Size Risk**: Outlier position sizes can threaten protocol liquidity.

These indicators together offer a nuanced, multi-dimensional risk profile.

---

## 📈 Results Summary

- **Wallets Analyzed**: 103
- **Average Score**: 161.6
- **Risk Categories**:
  - Low Risk: 100.0%
  - Medium/High Risk: 0.0%
- **Score Range**: 110 – 223
- **Highest Risk Wallet**: `0x427f2ac5fdf4245e027d767e7c3ac272a1f40a65` (Score: 223)
- **Lowest Risk Wallet**: `0x612a3500559be7be7703de6dc397afb541a16f7f` (Score: 110)

---

## 📂 Deliverables

- `wallet_risk_scores_YYYYMMDD_HHMMSS.csv`: Wallet ID + total score
- `wallet_risk_detailed_YYYYMMDD_HHMMSS.csv`: Score breakdown by dimension
- `wallet_risk_comprehensive_YYYYMMDD_HHMMSS.csv`: Raw features + scores
- `risk_analysis_report_YYYYMMDD_HHMMSS.txt`: Executive summary of findings

---

## 🧪 Next Steps

- Extend scoring to more active wallets and real-time data
- Calibrate weights based on institutional risk tolerance
- Introduce time-series modeling to capture evolving behaviors
- Deploy as a risk oracle or scoring API in live lending platforms

---

>⚠️ Note: Price data and health factor assumptions were approximated due to API constraints. This model serves as a foundational risk profiling system and can be fine-tuned with live data and market inputs.
