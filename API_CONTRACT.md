# MediCare API Specification

Interface specification between the FastAPI backend and React frontend.

Base URL: `http://localhost:8000`  
Headers: `Content-Type: application/json`, `Authorization: Bearer <token>` for protected endpoints.

---

## Authentication

### POST /auth/register
Registers a new patient or doctor account.
```json
// Request
{ 
  "phone": "9876543210", 
  "name": "Asha Rao", 
  "password": "secret", 
  "role": "patient" 
}
// role: "patient" | "doctor"

// Response (201 Created)
{ 
  "token": "<jwt>", 
  "user": { 
    "phone": "9876543210", 
    "name": "Asha Rao", 
    "role": "patient" 
  } 
}
```

### POST /auth/login
Authenticates an existing user.
```json
// Request
{ 
  "phone": "9876543210", 
  "password": "secret" 
}

// Response (200 OK)
{ 
  "token": "<jwt>", 
  "user": { 
    "phone": "9876543210", 
    "name": "Asha Rao", 
    "role": "patient" 
  } 
}
```

---

## Prescriptions

### POST /upload
Multipart form upload of a prescription image. Requires authenticated patient session.
- Field: `file` (`image/jpeg`, `image/png`, max 10MB)

```json
// Response (201 Created)
{
  "id": 12,
  "phone": "9876543210",
  "doctor_name": "Dr. Mehta",
  "hospital": "City Clinic",
  "date": "2026-06-15",
  "engine": "vision",
  "confidence": 0.87,
  "medicines": [
    { 
      "name": "Amoxicillin", 
      "dose": "500mg", 
      "frequency": "1-0-1", 
      "duration": "5 days", 
      "instructions": "after food" 
    }
  ]
}
```

### GET /patient/{phone}
Retrieves prescription history for a given patient phone number.
```json
// Response (200 OK)
{
  "phone": "9876543210",
  "name": "Asha Rao",
  "prescriptions": [
    {
      "id": 12, 
      "doctor_name": "Dr. Mehta", 
      "hospital": "City Clinic", 
      "date": "2026-06-15",
      "medicines": [ 
        { 
          "name": "Amoxicillin", 
          "dose": "500mg", 
          "frequency": "1-0-1", 
          "duration": "5 days", 
          "instructions": "after food" 
        } 
      ]
    }
  ]
}
```

---

## Clinical Summary & Interactions

### GET /summary/{phone}
Retrieves structured clinical summary for a patient. Requires authenticated doctor session.
```json
// Response (200 OK)
{
  "phone": "9876543210",
  "name": "Asha Rao",
  "summary": "Patient prescribed Amoxicillin 500mg (1-0-1, 5 days) on 2026-06-15 by Dr. Mehta.",
  "medicine_count": 1,
  "generated_at": "2026-06-17T12:40:00Z"
}
```

### GET /interactions/{phone}
Returns drug-drug interaction analysis across all prescriptions for the patient.
```json
// Response (200 OK)
{
  "phone": "9876543210",
  "name": "Asha Rao",
  "medicines_analyzed": ["Warfarin", "Ibuprofen"],
  "interactions": [
    {
      "medicines": ["Ibuprofen", "Warfarin"],
      "severity": "mild",
      "description": "Increased risk of bleeding with concurrent NSAID and anticoagulant use.",
      "sources": ["openfda"],
      "confidence": 0.82
    }
  ]
}
```

---

## Errors
- `400 Bad Request`: Invalid file or input format
- `401 Unauthorized`: Missing or expired token
- `403 Forbidden`: Insufficient permissions for requested role
- `404 Not Found`: Patient or resource not found
- `409 Conflict`: Phone number already registered
- `422 Unprocessable Entity`: Schema validation error
