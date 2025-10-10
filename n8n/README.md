# Inventory Manager Bot (n8n) — Quick README

## What it does
Telegram bot to **create / update / delete / show** inventory records, saving everything to **Google Sheets**. Always follows **preview → confirm → execute**.

## Prereqs
- n8n instance
- Telegram bot token (BotFather)
- Google Sheets access (Editor)
- One sheet/tab with columns:  
  `ID | Detalle | Cantidad | Precio_unitario | Total | Tipo_registro | Fecha | Fecha_creación | Última_actualización`

## Setup (high level)
1. **Credentials**  
   - Google: OAuth2 or Service Account (Editor on the spreadsheet).  
   - Telegram: Bot token.
2. **Config in nodes**  
   - Use **Spreadsheet ID** and exact **Sheet (tab) Name**.  
   - If Shared Drive, enable *Use Shared Drive*.  
   - Date format everywhere: **`YYYY/MM/DD`** (`{{$now.toFormat('yyyy/LL/dd')}}`).

## How it works
1. User sends a request (e.g., “cash sale: 5 pants at 50 today”).  
2. Bot **previews** (no UUID yet) and asks “Yes/No”.  
3. On **Yes**:  
   - Generate `UUID v4`.  
   - Compute `Total = Cantidad * Precio_unitario`.  
   - Normalize `Fecha` to `YYYY/MM/DD`.  
   - **Create/Update/Delete** in Google Sheets (delete = lookup by `ID` → delete row).  
4. Send **human-readable confirmation** (MarkdownV2-safe).

## Usage examples
- **Create**: “Add a cash sale: 5 pants at 50 today.”  
- **Update**: “Update ID `<uuid>` set Cantidad to 6.”  
- **Delete**: “Delete ID `<uuid>`.”  
- **Show**: “Show 2025/10/09.”

## Telegram formatting
Use `parse_mode=MarkdownV2` and escape special characters, or send plain text.

## Troubleshooting (top 3)
- **Forbidden / no permission**: wrong creds/scopes or missing Editor on the Sheet/Shared Drive.  
- **No records for today**: ensure `Fecha` in the sheet is stored as **text `YYYY/MM/DD`** and inputs are normalized.  
- **Delete by ID fails**: you must **lookup by ID → get row number → delete that row** (or soft delete).


