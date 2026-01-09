# Power BI Crypto Performance & Risk Dashboard

Power BI dashboard for exploring the **top 1000 cryptocurrencies** across market structure, performance, volatility, liquidity, and tokenomics (supply utilization & Volume/MarketCap).

The report is built as a 2-page interactive dashboard:

- **Page 1 – Crypto Market Overview**
- **Page 2 – Performance & Risk Explorer**

---

## 1. Project Overview

This project analyzes a snapshot of the top 1000 cryptocurrencies and turns it into an interactive Power BI report.

It helps you answer questions like:

- How is market cap distributed across **mega / large / mid / small caps**?
- How concentrated is the market in the **top 10 coins**?
- Which coins are **most volatile**, **most illiquid**, or **most “wrecked” vs ATH**?
- Which projects have **extreme tokenomics** (very low or very high circulating supply)?

---

## 2. Dataset

Files:

- `crypto_top1000_dataset.xlsx` – snapshot of the top 1000 coins with fields such as:
  - `market_cap_rank`, `name`, `symbol`
  - `current_price`, `market_cap`, `total_volume`
  - `price_change_percentage_1h`, `24h`, `7d`, `30d`, `1y`
  - `ath`, `ath_change_percentage`
  - `circulating_supply`, `total_supply`, `max_supply`
- `Crypto.pbix` – Power BI report built on top of the dataset.

> Source: https://www.kaggle.com/datasets/mihikaajayjadhav/top-1000-cryptocurrencies-real-time-data-2025

---

## 3. Dashboard Pages

### 3.1 Page 1 – Crypto Market Overview

High-level view of market structure and liquidity.

**Top row (KPIs & filters)**

- Slicers:
  - `Market Cap Tier` (Mega, Large, Mid, Small)
  - `Rank Top N` (slider on `market_cap_rank`)
  - `symbol` (searchable dropdown)
- Cards:
  - **Total Market Cap**
  - **Total Volume 24h**
  - **BTC Dominance %**
  - **Average 24h Change %**

**Middle row**

- **Market Cap Distribution by Tier**  
  Column chart showing **sum of `market_cap`** by `Market Cap Tier`.

- **Market Cap Share – Top 10 vs Others**  
  Donut / pie chart splitting market cap into **Top 10** and **Others**.

- **Market Cap vs Volume**  
  Bubble / scatter chart:
  - X: `market_cap`
  - Y: `total_volume`
  - Legend: `Market Cap Tier`
  - Tooltip: key metrics such as 24h change, Volume/MarketCap.

**Bottom row – Coin watchlist table**

Shows coin-level details with conditional formatting:

- `market_cap_rank`, `name`, `symbol`, `Market Cap Tier`
- `Market Cap %`, `Total_volume`
- `1h / 24h / 7d / 30d / 1y Change %`
- `Drawdown from ATH %`
- `Range 24h %` (intraday range)
- `supply_utilization`

Color scales and icons highlight winners/losers, deep drawdowns, and supply utilisation.

---

### 3.2 Page 2 – Performance & Risk Explorer

Deep dive into performance, volatility, and tokenomics.

**Top row**

- **Performance Heatmap (Matrix)**  
  - Rows: `crypto_name`  
  - Columns: timeframes `1h`, `24h`, `7d`, `30d`, `1y`  
  - Values: performance measure returning the appropriate `% change`  
  - Red–green color scale centered at 0%.

- **Distribution charts (histograms)**  
  - `price_change_percentage_24h` – up vs down coins on the day  
  - `Range 24h %` – distribution of intraday volatility  
  - `Drawdown from ATH %` – how “beat up” coins are vs their all-time highs

**Bottom row**

- **Tokenomics & Liquidity by Market Cap Tier**  
  Clustered/stacked bar:
  - Avg **Supply Utilization** (circulating vs max/total supply)
  - Avg **Volume to MarketCap** (24h volume ÷ market cap)

- **Extreme Tokenomics table**  
  Table filtered to coins where `supply_utilization` is:
  - **≥ 90%** (almost fully released), or
  - **≤ 20%** (heavily locked / future dilution risk)

  Shows `market_cap_rank`, `name`, `symbol`, `Market Cap Tier`, `market_cap`, `Volume to MarketCap`, `supply_utilization`, `Range 24h %`, `Drawdown from ATH %`, etc.

---

## 4. Key Measures & Calculations (examples)

Some of the core DAX logic used in the report:

```DAX
-- Market cap share
Market Cap % =
DIVIDE (
    SUM ( 'Crypto'[market_cap] ),
    CALCULATE ( SUM ( 'Crypto'[market_cap] ), ALL ( 'Crypto' ) )
)

-- Volume to MarketCap ratio
Volume to MarketCap :=
DIVIDE (
    SUM ( 'Crypto'[total_volume] ),
    SUM ( 'Crypto'[market_cap] )
)

-- Supply utilisation (0–100%)
Supply Utilization :=
DIVIDE (
    'Crypto'[circulating_supply],
    'Crypto'[max_supply]
)

-- Drawdown from ATH (% below all-time high)
Drawdown from ATH % :=
- 'Crypto'[ath_change_percentage]
