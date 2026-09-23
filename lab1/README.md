# Personal Investment Dashboard - Product Contract (Lab 1)

## Goal

Define a small Personal Investment Dashboard without designing its internal architecture or selecting technology, transforming a vague client request ("Help me follow my investments") into a bounded product contract.

---

## 1. Product research

Research Question: *How do existing products help a User follow market information, and which parts belong in this Dashboard's first version?*

| Product | Likely User and goal | Reusable pattern |
| ------- | -------------------- | ---------------- |
| **Google Finance** | Retail investor who wants quick, real-time market updates, stock watchlist tracking, and basic financial news. | Multi-asset watchlist display with daily price changes and status indicators for gainers/losers. |
| **TradingView** | Active trader or investor who needs in-depth technical analysis, custom charts, and real-time asset alerts. | Configurable price-change alerts and unified portfolio visualization across asset classes. |

### Research Evidence Summary
- **Adopted Patterns:** Unified watchlist for tracked assets and configurable threshold-based price alert criteria.
- **Deferred Patterns:** Complex technical indicators, social trading feeds, paper trading simulators, and execution/brokerage integrations.

---

## 2. Stakeholders and actors

### 1. Stakeholder Identification & Engagement Analysis

| Stakeholder | Motivation | Influence | Reason |
| ----------- | ---------- | --------- | ------ |
| **Retail Investor (User)** | High | High | Primary user whose decision-making and retention determine product adoption and success. |
| **Product Owner** | High | High | Sets product vision, prioritizes scope, and controls resources and requirements. |
| **Market Data Provider** | Low | High | External provider supplying price feeds; failure or terms change directly impacts product availability. |
| **Financial Regulator (e.g., SEC/ESMA)** | Low | High | Imposes data privacy, disclosure, and non-advisory compliance rules. |
| **System Administrator / Maintenance** | High | Low | Ensures platform uptime, basic system monitoring, and operational readiness. |

### 2. Stakeholder Matrix

| Motivation | Low influence | High influence |
| ---------- | ------------- | -------------- |
| **High**   | System Administrator / Maintenance | Retail Investor (User), Product Owner |
| **Low**    | *(None)* | Market Data Provider, Financial Regulator |

### 3. Actor Classification

- **Direct Human Actor:** Retail Investor (directly interacts with the dashboard to view portfolio and set alerts).
- **External System:** Market Data Provider (external service providing market prices and quote updates).
- **Other Stakeholders:** Product Owner, Financial Regulator, System Administrator.

---

## 3. Product promise and scope

### Product Promise
> **Personal Investment Dashboard** helps **individual retail investors** solve **fragmented asset tracking and delayed market awareness** so that **they can monitor their investment performance and receive timely price alerts in one place.**

### Goals
1. Allow an investor to create and manage a single personal watchlist of assets.
2. Display near-real-time price quotes and daily percentage changes for tracked assets.
3. Allow an investor to set price threshold alerts for specific assets.
4. Notify the investor when an asset crosses a user-defined price threshold.
5. Provide a summary view of total portfolio value based on user-entered asset quantities.

### Non-goals
1. Execute buy/sell orders or integrate with external brokerage accounts for automated trading.
2. Provide automated investment advice, AI portfolio recommendations, or robo-advisory services.
3. Support multi-currency auto-conversion or complex tax calculation reports.

---

## 4. Functional requirements

### DASH-1: Track Asset Watchlist
- **Actor Goal:** The Retail Investor needs to maintain a personalized list of financial assets to follow daily market movements.
- **User Story:** As a Retail Investor, I want to add and remove assets from my personal watchlist, so that I can focus only on investments relevant to me.
- **Definitions of Done:**
  - Shows the added asset with its current market symbol and price upon successful addition.
  - If the requested ticker/symbol is invalid or unsupported, display a clear "Asset not found or unsupported" message and do not modify the watchlist.
  - Limits the maximum number of tracked assets per user watchlist to 50 items.

### DASH-2: View Near-Real-Time Quotes
- **Actor Goal:** The Retail Investor needs to observe up-to-date prices and percentage changes for tracked assets.
- **User Story:** As a Retail Investor, I want to view current asset prices and daily gain/loss percentages, so that I can evaluate market trends.
- **Definitions of Done:**
  - Displays the asset price, daily price change, and percentage change refreshed upon user request or schedule.
  - If market data is unavailable or connection to the data provider fails, display a "Data Stale / Market Data Unavailable" banner without showing false zero values.
  - Restricts data display to supported public market hours or clearly marks off-market prices as "Last Closed Price".

### DASH-3: Set Price Alerts
- **Actor Goal:** The Retail Investor needs to set target price thresholds to receive notifications when key levels are reached.
- **User Story:** As a Retail Investor, I want to configure high or low target price alerts for a specific asset, so that I don't have to constantly monitor the screen.
- **Definitions of Done:**
  - Confirms alert creation with the asset symbol, threshold value, and condition (Above/Below).
  - If the entered threshold price is zero or negative, display a validation error and reject the alert creation.
  - Limits active alerts to a maximum of 10 per individual asset.

### DASH-4: Trigger Alert Notification
- **Actor Goal:** The Retail Investor needs to be informed immediately when an asset hits a set price target.
- **User Story:** As a Retail Investor, I want to receive a clear notification when a price threshold is crossed, so that I can make timely financial decisions.
- **Definitions of Done:**
  - Triggers an in-app alert indicator showing the asset, threshold breached, and exact time of breach.
  - If market data is delayed or missing, delay the notification and flag it as "Delayed Alert" rather than generating a false positive.
  - Automatically marks triggered alerts as "Triggered" to prevent duplicate notifications for the same event.

### DASH-5: Calculate Portfolio Value Summary
- **Actor Goal:** The Retail Investor needs to see the aggregate value of their holdings.
- **User Story:** As a Retail Investor, I want to enter my holding quantities for tracked assets and view total portfolio value, so that I can understand my total investment posture.
- **Definitions of Done:**
  - Calculates total portfolio value by multiplying user-entered quantities by the latest available asset prices.
  - If price data for one or more holdings is missing or stale, calculate the summary using available data and explicitly display a "Partial Evaluation: Some price feeds are offline" warning.
  - Restricts calculations to non-negative asset quantities.

---

## 5. C4 System Context

### System Context Diagram

![C4 System Context View](c4-system-context.png)

```mermaid
flowchart LR
    investor["Retail Investor\n[Human Actor]"] -->|"Configures watchlists, sets price alerts, enters holdings, and views portfolio metrics"| dashboard["Personal Investment Dashboard\n[System of Interest]"]
    dashboard -->|"Requests market quotes, security metadata, and price feed updates"| marketData["Market Data Provider\n[External System]"]
    marketData -->|"Returns price quotes, ticker status, or unavailable error"| dashboard
    dashboard -->|"Sends push/email notifications for triggered price alerts"| notificationService["External Notification Provider\n[External System]"]
    notificationService -->|"Delivers alert messages or returns delivery status"| dashboard
