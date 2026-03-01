# Medical Scribe Pipeline Logic

This file defines how Reilly (the agent) handles clinical notes received via WhatsApp/Telegram.

## 1. Intent Detection (Trigger)
When a message (text or transcribed voice) arrives from Pastor, check for keywords:
- **Ingreso:** "ingreso", "admisión", "admitir".
- **Evolución:** "evolución", "evolutiva", "nota diaria", "seguimiento".
- **Recibimiento:** "recibimiento", "recibir paciente", "entrega".

## 2. Context Retrieval & Global Headers
- Reilly automatically populates global fields in every template:
    - `{{ fecha }}`: Current date (e.g., 01/03/2026).
    - `{{ hora }}`: Current time (e.g., 00:30).
    - `{{ doctor_nombre }}`: "Dr. Pastor Soto".
    - `{{ especialidad }}`: "Medicina Interna (Residente)".
- Use `read_docx.py` to read the relevant template from `templates/`.

## 3. Extraction & Global Metadata Mapping
- **Identity:** `{{ nombre_paciente }}`, `{{ cedula }}`, `{{ edad }}`, `{{ sexo }}`.
- **Vitals:** `{{ ta }}`, `{{ fc }}`, `{{ fr }}`, `{{ temp }}`, `{{ sao2 }}`.
- **Logic:** Map the transcribed text to the fields identified in the template.
- Use Spanish medical terminology.
- Maintain a "Draft State" for the current patient.

## 4. Gap Check & Proactive Suggestions
- Mandatory fields: If missing, Reilly MUST ask.
- **Proactive Filling:** Reilly will suggest optional fields based on clinical context (e.g., asking for specific neurological reflexes in a stroke case).
- **Format Suggestions:** Based on the diagnosis, Reilly will suggest additional templates (e.g., "Note of Central Line Procedure" if the patient is unstable).

## 5. Finalization
- Render data into the literal original DOCX templates using `docxtpl`.
- Preserve all logos, headers, and formatting.
