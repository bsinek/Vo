# ScribeAgent AI — Full-Stack Medical Transcription Platform

AI-powered medical transcription system with a FastAPI backend and Next.js frontend.  
Processes medical dictation audio and returns structured clinical outputs including transcription, SOAP notes, billing codes, prescriptions, and lab orders.

Devpost: https://devpost.com/software/vo  
YouTube Demo: https://www.youtube.com/watch?v=DvakOibwVCo&embeds_referring_euri=https%3A%2F%2Fdevpost.com%2F

---

## Overview

The platform supports both real-time audio streaming and file-based transcription workflows.

Core capabilities:

- Live audio streaming via WebSocket
- File upload processing for recorded dictations
- Structured SOAP note generation
- ICD-10 billing code lookup
- Prescription extraction
- Lab order extraction
- Web-based UI for interaction

---

## Quick Start

### 1. Install backend dependencies

```bash
pip install -r requirements.txt
```

### 2. Start backend server

```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

API will be available at:

```
http://localhost:8000
```

### 3. Install frontend dependencies

```bash
npm install
```

### 4. Start frontend server

```bash
npm run dev
```

Frontend will be available at:

```
http://localhost:3000
```

---

## Testing the Application

### Web interface

- Open `http://localhost:3000`
- Choose streaming or file upload mode
- Test live transcription or upload audio

### API tests

HTTP endpoint:

```bash
python test_api.py
```

WebSocket streaming endpoint:

```bash
python test_websocket.py
```

---

## API

### WebSocket `/ws/process-visit`

Real-time streaming endpoint.

Connection:

```
ws://localhost:8000/ws/process-visit
```

Flow:

1. Client sends binary audio chunks  
2. Server returns partial transcription updates  
3. Client sends `"END_OF_STREAM"`  
4. Server returns final structured result  

Example partial response:

```json
{
  "type": "partial_transcript",
  "text": "Patient is a 34-year-old...",
  "is_final": false
}
```

Example final response:

```json
{
  "type": "final_result",
  "data": {
    "transcription": "...",
    "soap_note": "...",
    "diagnosis": "...",
    "billing_code": {
      "code": "J02.0",
      "description": "streptococcal pharyngitis"
    },
    "prescriptions": [...],
    "lab_orders": [...]
  }
}
```

---

### POST `/api/process-visit`

File upload endpoint.

- Method: POST  
- Content-Type: multipart/form-data  
- Body: audio file  

Returns the same structured result as the streaming endpoint.

---

### GET `/`

Health check endpoint.

---

## Project Structure

```
├── main.py
├── requirements.txt
├── data/
│   └── icd10_codes.csv
├── test_api.py
├── test_websocket.py
└── README.md
```

---

## Implementation Notes

Current system uses a mix of mock and real components.

Mock components:

- Speech-to-text output
- Entity extraction
- SOAP note generation

Real components:

- ICD-10 billing code lookup
- File upload handling
- WebSocket streaming pipeline
- Async processing
- CORS configuration for frontend integration

---

## Deployment

Backend can be deployed to:

- Render
- Google Cloud Run
- Heroku
- AWS (minor adjustments required)

---

## Extending with Real AI Services

Replace mock functions in `main.py`:

- `transcribe_audio_mock()` → speech-to-text API  
- `extract_entities_mock()` → LLM-based NER pipeline  
- `generate_soap_note_mock()` → LLM SOAP generation  

---

## Performance

- ~500ms response time using mock pipeline
- Supports multiple audio formats
- In-memory ICD-10 lookup
- Concurrent WebSocket processing

---

## Error Handling

- File validation for uploads
- CSV loading fallbacks
- Billing lookup default handling
- Standard HTTP status codes
