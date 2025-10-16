# Crypto Portfolio Intelligence (English / Spanish)

**Crypto Portfolio Intelligence** is an **n8n** workflow that performs an automated financial analysis of a cryptocurrency portfolio.  
It combines live market data, exchange rates, and market sentiment (Fear & Greed Index), and sends a bilingual report (English and Spanish) to Telegram.

---

## Overview

The workflow performs the following steps:

1. **Fetch cryptocurrency prices from CoinGecko**
   - Endpoint: `https://api.coingecko.com/api/v3/simple/price`
   - Configured coins: Bitcoin, Ethereum, Cardano, Solana, etc.
   - Parameters: `vs_currency=usd`, `include_24hr_change=true`, `include_24hr_vol=true`

2. **Get USD→EUR exchange rate**
   - From an external provider such as Exchangerate API or Open Exchange Rates.
   - Used to convert portfolio value to euros.

3. **Retrieve the Fear & Greed Index**
   - Source: [https://api.alternative.me/fng/?limit=7](https://api.alternative.me/fng/?limit=7)
   - The parser processes the JSON format:
     ```json
     {
       "name": "Fear and Greed Index",
       "data": [
         { "value": "34", "value_classification": "Fear", "timestamp": "..." }
       ]
     }
     ```
   - Calculates the current index, 7-day average, trend, and number of "fear" or "greed" days.

4. **Process the portfolio data**
   - Input: JSON or CSV (from Google Sheets, Set node, or API)
   - Example format:
     ```json
     [
       { "Portafolio": "bitcoin", "quantity": 5, "buy_price": 190, "currency": "USD", "state": "registered" },
       { "Portafolio": "ethereum", "quantity": 3, "buy_price": 370, "currency": "USD", "state": "registered" }
     ]
     ```
   - Calculates each asset’s value, profit/loss (PnL), and weighted 24-hour performance.

5. **Run the `Code (JavaScript)` node**
   - Cleans and normalizes all input data.
   - Calculates:
     - Total portfolio value (EUR)
     - PnL absolute and percentage
     - 24-hour performance (€ and %)
     - Weekly Fear & Greed average and trend
   - Example output:
     ```json
     {
       "portfolio": { "totalEUR": 488854.35, "changePercent": -0.85 },
       "sentiment": { "today": { "index": 28, "label": "Fear" } }
     }
     ```

6. **Send the results to OpenAI (GPT-5 or equivalent)**
   - Uses a System Message that defines the financial analyst role and output structure.
   - Generates two reports:
     - Spanish version
     - English version

7. **Send the bilingual report to Telegram**
   - Sent through `Telegram > Send Message`
   - Markdown format example:
     ```
     ES:
     1. Resumen del Desempeño General: ...
     2. Observación Clave: ...
     3. Recomendación Específica: ...
     4. Perspectiva de Mercado: ...

     EN:
     1. Overall Performance Summary: ...
     2. Key Observation: ...
     3. Specific Recommendation: ...
     4. Market Outlook: ...
     ```

---

## Node Structure

| Step | Node | Purpose |
|------|------|----------|
| 1 | **HTTP Request – CoinGecko** | Get live crypto prices |
| 2 | **HTTP Request – FX Rate** | Get USD→EUR rate |
| 3 | **HTTP Request – Fear & Greed** | Get market sentiment |
| 4 | **Code – Parser (F&G)** | Parse JSON if returned as string |
| 5 | **Set / Google Sheet** | Define portfolio input |
| 6 | **Merge (Append)** | Combine all data sources |
| 7 | **Code – Main Analysis** | Perform calculations and summaries |
| 8 | **OpenAI Node** | Generate bilingual report |
| 9 | **Telegram – Send Message** | Send final message |

---

## System Message Summary

- Generate **two reports**: first in Spanish, then in English.  
- Each report must include:
  1. Overall performance summary  
  2. Key daily observation  
  3. Single actionable recommendation (buy, hold, sell)  
  4. Market outlook using the Fear & Greed Index  
- Limit to **150 words per language**.  
- Use only provided data.  
- Format with Markdown.

---

## Requirements

- Active **n8n.cloud** account or self-hosted version ≥ 1.50  
- **CoinGecko API** (public or key)  
- **FX rate API** such as Exchangerate.host  
- **Alternative.me (Fear & Greed)** public API access  
- **Telegram Bot Token**  
- **OpenAI API credentials**

---

## Example Output (from Code node)

```json
{
  "portfolio": {
    "totalEUR": 488854.35,
    "change24hEUR": -4189.12,
    "changePercent": -0.85,
    "totalProfitEUR": 485555.79,
    "totalProfitPercent": 14720.24
  },
  "sentiment": {
    "today": { "index": 28, "label": "Fear" },
    "week": { "avg": 36, "trendPct": -15.2 }
  },
  "summary": "Portfolio: €488,854 | 24h: -0.85% (€-4,189) | PnL: €485,556 | F&G: 28 (Fear)"
}
