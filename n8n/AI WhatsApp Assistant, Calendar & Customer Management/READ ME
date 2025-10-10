# WhatsApp AI Assistant – Calendar and Client Management

## Overview
This project is an **n8n automation workflow** that connects WhatsApp messages with AI-powered processing, Google Calendar, Google Sheets, and Gmail.  

It enables an intelligent assistant to:
- Manage **client and supplier contact data** stored in Google Sheets.
- Handle **calendar events** such as creating, rescheduling, canceling, or checking appointments.
- Process **different types of WhatsApp inputs**: text, voice messages, and images (with OCR/vision analysis).
- Use **AI models** (OpenAI and Google Gemini) for understanding and responding naturally.
- Send **automated replies via WhatsApp**, in both text and audio formats.
- Trigger **email sending** through Gmail.

---

## Features
- **WhatsApp Integration**
  - Triggers on new incoming WhatsApp messages.
  - Supports text, voice, and image messages.
- **AI Agent**
  - Fuses user input (text > audio transcription > image analysis).
  - Detects **intents**: contacts management, event management, or email sending.
  - Provides natural responses in plain text.
- **Contact Management (Google Sheets)**
  - Lookup, insert, update, or delete contact records.
  - Schema includes:  
    `ID, Tipo, Nombre, Empresa, Email, Telefono, Direccion, Ciudad, Pais, NIF_CIF, Categoria, Notas, Actualizado_En`.
- **Calendar Management (Google Calendar)**
  - Create, reschedule, cancel, and query events.
  - Default timezone: `Europe/Madrid`.
  - Default duration: **30 minutes**.
- **Email Integration (Gmail)**
  - Sends emails to contacts stored in Google Sheets.
- **Multimodal Processing**
  - **Voice**: audio transcription with OpenAI Whisper.
  - **Images**: AI-powered image analysis with OpenAI Vision.
  - **Memory**: session context preserved across conversations.

---

## Tech Stack
- **n8n** (workflow automation)
- **WhatsApp Business API**
- **OpenAI GPT-4o / Whisper / TTS**
- **Google Gemini API**
- **Google Sheets API**
- **Google Calendar API**
- **Gmail API**

---

## How It Works
1. **Trigger**: A user sends a WhatsApp message (text, voice, or image).
2. **Input Processing**:
   - Text → used directly.
   - Voice → transcribed into text.
   - Image → analyzed and described.
3. **AI Agent**:
   - Builds a merged `TEXT_FUSION`.
   - Determines the **intent** (e.g., add client, check appointment).
   - Executes the appropriate tool (Sheets, Calendar, Gmail).
4. **Response**:
   - Generates a natural language reply.
   - Sends back via WhatsApp (text or audio).
   - Ensures one clear, human-like response.  
     Example:  
     > "Your appointment with Lissett Gamboa has been scheduled for October 11, 2025, from 08:00 to 08:30."

---

## Setup
1. Import the JSON workflow into **n8n**.
2. Configure credentials for:
   - WhatsApp Business API
   - OpenAI API
   - Google Gemini API
   - Google Sheets
   - Google Calendar
   - Gmail
3. Update references to your Google Sheet (`Plantilla_Contactos__Clientes_Proveedores`) and default Calendar.
4. Activate the workflow.

---

## Example Use Cases
- **Schedule a meeting**:  
  “Schedule a meeting with Juan Pérez tomorrow at 10am” → Adds event in Google Calendar.  
- **Update a contact**:  
  “Update María López’s phone number to +34 600 000 000” → Updates Google Sheets.  
- **Send email**:  
  “Send email to Carlos with subject ‘Invoice’ and attach details” → Sends Gmail email.  
- **Voice note**:  
  Voice message → Transcribed, intent detected, action executed.  
- **Image**:  
  Image with text → OCR + AI description processed.  

---

## Notes & Limitations
- Default timezone: **Europe/Madrid** (configurable).
- Duration default: **30 minutes**.
- If mandatory fields are missing, the assistant will ask for clarification.
- Multiple matches require confirmation before update/delete.
- Responses are **always plain text** for clarity.

---
