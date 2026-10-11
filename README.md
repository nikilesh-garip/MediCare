# MediCare

Prescription management and patient clinical tracking platform.

**Core Workflow:**
1. **Patient**: Uploads a prescription image to digitize medicine data (name, dosage, frequency, duration, and instructions).
2. **Doctor**: Searches a patient by phone number to access their consolidated medicine history, clinical notes, and treatment summaries.
3. **Safety Analysis**: Cross-references medicines against FDA drug labels and interaction databases to flag potential drug-drug conflicts.

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | React 18, Vite, Three.js, react-router-dom |
| **Backend** | Python FastAPI, SQLAlchemy 2.0, Pydantic |
| **Database** | SQLite (default dev) / PostgreSQL compatible |
| **Auth** | JWT (python-jose), bcrypt password hashing |
| **OCR & Vision** | Tesseract OCR + Vision processing |
| **Drug Safety** | openFDA API + Association rule analysis |

---

## Getting Started

### 1. Backend Setup (Port 8000)

```bash
cd backend
python -m venv .venv

# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
copy .env.example .env    # On macOS/Linux: cp .env.example .env

# Optional: seed initial demo data
python seed.py

# Start backend server
uvicorn app.main:app --reload
```

- API Documentation: [http://localhost:8000/docs](http://localhost:8000/docs)
- Health Check: [http://localhost:8000/health](http://localhost:8000/health)

### 2. Frontend Setup (Port 5173)

```bash
cd frontend
npm install
copy .env.example .env    # On macOS/Linux: cp .env.example .env
npm run dev
```

- Application UI: [http://localhost:5173](http://localhost:5173)

---

## Demo Accounts

Initial seeded accounts (all passwords: `demo1234`):

- **Doctor Account**: `9000000001`
- **Patient Accounts**: `9876543210`, `9123456780`, `9555500000`

---

## Project Structure

```
MediCare/
├── API_CONTRACT.md          # Frontend/Backend API specification
├── ARCHITECTURE.md          # System architecture & interaction detection pipeline
├── backend/
│   ├── app/
│   │   ├── main.py          # FastAPI application entry & middleware
│   │   ├── config.py        # Environment settings
│   │   ├── database.py      # SQLAlchemy engine & session setup
│   │   ├── models.py        # Database models (User, Prescription, Medicine)
│   │   ├── schemas.py       # Pydantic schemas
│   │   ├── auth.py          # Authentication & JWT verification
│   │   ├── ocr.py           # OCR text extraction
│   │   ├── interaction_engine.py # Drug interaction detection engine
│   │   └── routers/         # API endpoints (auth, prescriptions, summary, notes)
│   ├── requirements.txt
│   └── seed.py              # Test database seeding script
└── frontend/
    ├── src/
    │   ├── api.js           # API client
    │   ├── App.jsx          # Route configuration
    │   ├── pages/           # Application views (Login, Patient, Doctor, About)
    │   └── components/      # UI components (BodyMap, DnaParticles, PrescriptionList)
    └── package.json
```

---

## Drug Interaction Detection

When patients log multiple prescriptions, MediCare checks for potential drug-drug interactions:

- **FDA Drug Label Verification**: Scans openFDA contraindication and adverse reaction data.
- **Co-Prescription Pattern Analysis**: Evaluates co-occurrence patterns across prescription records.
- **Clinical Synthesis**: Deduplicates and ranks identified interactions by severity (Mild, Moderate, Severe) with clinical context.
