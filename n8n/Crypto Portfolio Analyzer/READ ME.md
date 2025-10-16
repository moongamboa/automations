# 💹 Crypto Portfolio Intelligence (ES/EN)

**Crypto Portfolio Intelligence** es un workflow de **n8n** que realiza análisis financiero automatizado de un portafolio de criptomonedas, combinando datos de mercado en tiempo real, tipo de cambio y sentimiento del mercado (Fear & Greed Index).  
El resultado se envía automáticamente a **Telegram** en formato bilingüe (Español / Inglés).

---

## 🚀 Funcionalidad

El flujo ejecuta los siguientes pasos:

1. **Obtiene precios de CoinGecko**
   - Endpoint: `https://api.coingecko.com/api/v3/simple/price`
   - Monedas configuradas: *Bitcoin, Ethereum, Cardano, Solana, etc.*
   - Parámetros: `vs_currency=usd`, `include_24hr_change=true`, `include_24hr_vol=true`

2. **Obtiene el tipo de cambio USD→EUR**
   - De un proveedor externo (p. ej. Exchangerate API, Open Exchange Rates, etc.)
   - Permite expresar el valor total del portafolio en EUR.

3. **Recupera el índice de miedo y codicia (Fear & Greed Index)**
   - Fuente: [`https://api.alternative.me/fng/?limit=7`](https://api.alternative.me/fng/?limit=7)
   - El parser JSON procesa la estructura:
     ```json
     {
       "name": "Fear and Greed Index",
       "data": [{ "value": "34", "value_classification": "Fear", "timestamp": "..." }]
     }
     ```
   - Se calcula el índice actual, promedio semanal, tendencia y número de días de “miedo” y “codicia”.

4. **Procesa el portafolio**
   - Entrada: CSV o JSON (de Google Sheets, Set node o API propia)
   - Campos esperados:
     ```json
     [
       { "Portafolio": "bitcoin", "quantity": 5, "buy_price": 190, "currency": "USD", "state": "registered" },
       { "Portafolio": "ethereum", "quantity": 3, "buy_price": 370, "currency": "USD", "state": "registered" }
     ]
     ```
   - Calcula valor actual, PnL, y rendimiento 24h ponderado.

5. **Ejecuta el nodo `Code (JavaScript)`**
   - Normaliza los datos.
   - Calcula métricas agregadas:
     - Valor total (€)
     - PnL absoluto y porcentual
     - Variación 24h (€ y %)
     - Promedio semanal del *Fear & Greed*
   - Genera un objeto JSON de salida como:
     ```json
     {
       "portfolio": { "totalEUR": 488854.35, "changePercent": -0.85, "totalProfitPercent": 14720.24 },
       "sentiment": { "today": { "index": 28, "label": "Fear" } }
     }
     ```

6. **Envía el resultado a OpenAI (GPT-5 o equivalente)**
   - **System Message:** describe el rol del analista financiero y formato esperado.
   - El modelo genera dos versiones del análisis:
     - Español 🇪🇸  
     - Inglés 🇬🇧  

7. **Envía el informe a Telegram**
   - A través de `Telegram > Send Message`
   - En formato Markdown con ambos idiomas:
     ```
     🇪🇸 **Análisis del Portafolio Cripto**
     1. Resumen del Desempeño General: …
     2. Observación Clave: …
     3. Recomendación Específica: …
     4. Perspectiva de Mercado: …

     🇬🇧 **Crypto Portfolio Analysis**
     1. Overall Performance Summary: …
     2. Key Observation: …
     3. Specific Recommendation: …
     4. Market Outlook: …
     ```

---

## 🧩 Estructura de Nodos

| Etapa | Nodo | Función |
|-------|------|----------|
| 1 | **HTTP Request – CoinGecko** | Recupera precios actuales |
| 2 | **HTTP Request – FX Rate** | Obtiene conversión USD→EUR |
| 3 | **HTTP Request – Fear & Greed** | Sentimiento del mercado |
| 4 | **Code – Parser F&G** | Convierte string → JSON válido |
| 5 | **Set / Google Sheet** | Portafolio de criptomonedas |
| 6 | **Merge (Append)** | Une todas las fuentes de datos |
| 7 | **Code – Main JavaScript** | Análisis, cálculos y resumen |
| 8 | **OpenAI Node (ChatGPT)** | Redacción del análisis bilingüe |
| 9 | **Telegram – Send Message** | Envía el reporte final |

---

## 🧠 System Message (resumen)

> Genera análisis **en español e inglés** con:  
> 1️⃣ Desempeño general  
> 2️⃣ Observación clave  
> 3️⃣ Recomendación  
> 4️⃣ Perspectiva de mercado (Fear & Greed)  
>  
> - Sin inventar datos.  
> - 150 palabras por idioma.  
> - Formato Markdown.  
> - Tono profesional y claro.

---

## ⚙️ Requisitos

- Cuenta activa en **n8n.cloud** o instalación local ≥ v1.50  
- API key de **CoinGecko** (opcional si usas pública)  
- API para tipo de cambio (exchangerate.host o similar)  
- Acceso al endpoint público de **Alternative.me (F&G)**  
- Token de **Telegram Bot**  
- Credenciales de **OpenAI**

---

## 🧾 Salida esperada

**Ejemplo de output JSON (del nodo Code):**
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

