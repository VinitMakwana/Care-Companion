# 🩺 Care Companion — Multimodal AI Healthcare Assistant

> **An AI-powered healthcare companion that understands text, voice, medical prescriptions, and images while providing knowledge-grounded, multilingual assistance and medication reminders.**

Care Companion is a **multimodal and multilingual healthcare AI web application** built with **Python, Flask, SQLite, ChromaDB, RAG, and Gemini**.

The system combines conversational AI with healthcare-focused capabilities such as:

- 💬 AI healthcare conversations
- 📚 Retrieval-Augmented Generation (RAG)
- 💊 Prescription understanding
- 🖼️ Medical/skin image analysis
- 🎙️ Voice-to-text interaction
- 🌐 Multilingual responses
- 👤 Personalized health profiles
- 💾 Persistent chat history
- 💊 Medication scheduling
- 📧 Email medication reminders
- 🛡️ Healthcare safety guardrails

The application is designed as an **AI healthcare assistant prototype** and is not intended to replace qualified medical professionals.

---

# 🌟 Why Care Companion?

Traditional healthcare chatbots primarily support text-based conversations.

Care Companion takes a more comprehensive approach by combining **LLM reasoning + RAG + multimodal inputs + personalization + medication management** into a single web application.

```text
                    CARE COMPANION
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
        ▼                 ▼                 ▼
     TEXT              VOICE              IMAGE
        │                 │                 │
        │                 ▼                 ├──────────────┐
        │          Faster-Whisper           │              │
        │                                   ▼              ▼
        │                              Prescription    Skin Image
        │                              Analysis         Analysis
        │                                   │              │
        └─────────────────┬─────────────────┘              │
                          ▼                                │
                   Healthcare AI                           │
                          │                                │
             ┌────────────┼────────────┐                   │
             ▼            ▼            ▼                   │
            RAG         Gemini      User Profile           │
             │            │            │                   │
             └────────────┼────────────┘                   │
                          ▼                                │
                  Personalized Response ◄──────────────────┘
                          │
              ┌───────────┴───────────┐
              ▼                       ▼
       Chat History             Medication
                                  Scheduler
                                      │
                                      ▼
                              Email Reminders
```

---

# ✨ Key Features

## 💬 1. AI Healthcare Chat

Users can communicate with the assistant through natural-language text.

The application:

1. Accepts the user's query.
2. Loads the user's health profile.
3. Uses the configured LLM handler.
4. Retrieves relevant knowledge through RAG when applicable.
5. Generates a response in the user's preferred language.
6. Stores the conversation in SQLite.

The implementation maintains conversation threads and stores both user and assistant messages with metadata such as sources and RAG references.

---

# 📚 2. Retrieval-Augmented Generation

Care Companion uses **RAG** to ground healthcare responses in documents stored inside the `knowledge_base/` directory.

Supported knowledge-base formats include:

```text
PDF
TXT
MD
```

The application recursively scans the knowledge base, processes documents into chunks, and stores/retrieves them through **ChromaDB**.

### RAG Pipeline

```text
Healthcare Documents
        │
        ▼
 Document Loader
        │
        ▼
 Text Extraction
        │
        ▼
 Chunking
        │
        ▼
 Embeddings
        │
        ▼
 ChromaDB
        │
        ▼
 Similarity Retrieval
        │
        ▼
 Relevant Context
        │
        ▼
 Gemini / LLM
        │
        ▼
 Grounded Response
```

This approach helps reduce dependence on the model's general knowledge by providing relevant context from the application's healthcare knowledge base.

---

# 💊 3. Prescription Understanding

Users can upload a prescription image or PDF.

The application:

```text
Prescription
     │
     ▼
File Upload
     │
     ├───────────────┐
     ▼               ▼
PDF Extraction     Image
     │               │
     └───────┬───────┘
             ▼
     AI Prescription
         Analysis
             │
             ▼
      Medicine Details
             │
     ┌───────┼────────┐
     ▼       ▼        ▼
 Medicine  Dosage  Frequency
     │
     ▼
 Instructions
     │
     ▼
 Precautions
```

The backend extracts text from PDF prescriptions when possible and passes the document/image information to the LLM handler for analysis.

The resulting information can be stored in the conversation as structured **medicine cards**.

---

# 🖼️ 4. Medical Image / Skin Image Analysis

Care Companion supports image-based healthcare interactions.

Users can upload an image and provide a question about it.

```text
User Image
     │
     ▼
Image Upload
     │
     ▼
AI Vision Analysis
     │
     ├── User Profile
     ├── User Question
     └── Healthcare Context
     │
     ▼
Healthcare Response
     │
     ▼
Chat History
```

The application provides a dedicated `/api/upload-skin-image` endpoint for image-based analysis and stores the interaction as part of the user's conversation thread.

> **Important:** Image analysis is intended for informational assistance and should not be treated as a clinical diagnosis.

---

# 🎙️ 5. Voice Interaction

Care Companion supports voice-based interaction through **Faster-Whisper**.

### Voice Pipeline

```text
User Speech
     │
     ▼
Audio Upload
     │
     ▼
Faster-Whisper
     │
     ▼
Speech → Text
     │
     ▼
Healthcare AI
     │
     ▼
Response
```

The application uses the Faster-Whisper `tiny` model with CPU/int8 configuration for transcription.

The `/api/voice-to-text` endpoint processes uploaded audio, transcribes it, sends the resulting text to the AI system, and stores the conversation.

---

# 🌐 6. Multilingual Healthcare Assistance

Users can select a preferred language through their profile.

The selected language is passed to the AI response layer so that responses can be generated according to the user's language preference.

```text
User Profile
     │
     ▼
Preferred Language
     │
     ▼
User Query
     │
     ▼
LLM Handler
     │
     ▼
Localized Healthcare Response
```

The application maintains the preferred language as part of the user's profile and uses it across text, image, prescription, and voice interactions.

---

# 👤 7. Personalized Health Profile

During initial setup, users can create a healthcare profile containing information such as:

- Full name
- Age
- Weight
- Height
- Email
- Contact information
- Emergency contact
- Preferred language
- Health conditions
- Allergies

This profile can be used to personalize interactions with the AI assistant.

---

# 💾 8. Persistent Chat History

Care Companion supports persistent conversations using SQLite.

The database maintains:

### Users

```text
users
```

### Health Profiles

```text
profiles
```

### Medication Schedules

```text
medication_schedule
```

### Chat Threads

```text
chat_threads
```

### Chat Messages

```text
chat_history
```

The application supports multiple conversation threads and associates messages with their corresponding thread.

---

# 💊 9. Smart Medication Scheduler

Users can create medication schedules containing:

- Medicine name
- Dosage
- Frequency
- Duration
- Instructions
- Precautions
- Medication times
- Start date
- End date

The schedule is persisted in SQLite.

### Scheduler Architecture

```text
Medication Details
        │
        ▼
   SQLite Database
        │
        ▼
   APScheduler
        │
        ▼
 Check Current Time
        │
        ▼
 Scheduled Medication?
       / \
     Yes  No
      │    │
      ▼    └── Continue Monitoring
 Send Email
 Reminder
```

The scheduler checks active medication schedules periodically and sends reminders when a scheduled time matches the current time.

---

# 📧 10. Email Notifications

The project supports SMTP-based email notifications.

Emails can be used for:

- Medication schedule confirmation
- Medication reminders
- Test email functionality

When a medication schedule is created, the application can immediately send a schedule confirmation email to the user's profile email.

---

# 🛡️ 11. Healthcare Safety Guardrails

The project includes a dedicated:

```text
utils/guardrails.py
```

module for healthcare-oriented safety handling.

The goal is to prevent the assistant from behaving like an unrestricted medical diagnostic system and to encourage appropriate professional medical consultation when necessary.

---

# 🔐 12. Authentication & User Sessions

Care Companion includes:

- User registration
- User login
- Password hashing
- Session-based authentication
- Logout
- Protected application routes

Passwords are stored using Werkzeug password hashing rather than plain-text storage.

---

# 🏗️ System Architecture

```text
                         ┌─────────────────────┐
                         │      Web Client     │
                         │   HTML/CSS/JS       │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    Flask Backend    │
                         │      app.py         │
                         └──────────┬──────────┘
                                    │
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
              ▼                     ▼                     ▼
       Authentication         Healthcare AI          File Processing
              │                     │                     │
              ▼                     ▼                     ├── PDF
           SQLite                Gemini                  ├── Images
              │                     │                     └── Audio
              │                     │
              │              ┌──────┴──────┐
              │              ▼             ▼
              │            RAG          Guardrails
              │              │
              │              ▼
              │          ChromaDB
              │
              ├──────── Medication Scheduler
              │                  │
              │                  ▼
              │               SMTP
              │                  │
              │                  ▼
              │             Email Alerts
              │
              └──────────── Chat History
```

The Flask application initializes the SQLite database, ChromaDB knowledge base, RAG loader, LLM handler, and background scheduler during startup.

---

# 📂 Project Structure

```text
Care-Companion/
│
├── chroma_storage/
│   └── ChromaDB persistent storage
│
├── knowledge_base/
│   └── Healthcare knowledge documents
│       ├── PDF
│       ├── TXT
│       └── MD
│
├── templates/
│   ├── landing.html
│   ├── login.html
│   ├── register.html
│   ├── disclaimer.html
│   ├── setup_profile.html
│   ├── chat.html
│   ├── medications.html
│   └── ...
│
├── uploads/
│   └── User uploaded files
│
├── utils/
│   ├── guardrails.py
│   ├── chroma_manager.py
│   ├── llm_handler.py
│   ├── prescription_parser.py
│   ├── email_notifier.py
│   └── rag_engine.py
│
├── app.py
├── care_companion.db
├── email_log.txt
├── ingestion_log.json
├── requirements.txt
├── test_rag.py
├── .gitignore
└── README.md
```

The current repository contains dedicated directories for ChromaDB storage, the healthcare knowledge base, templates, uploads, and utility modules, with `app.py` acting as the primary Flask application entry point.

---

# 🧩 Technology Stack

| Category | Technology |
|---|---|
| Backend | Python, Flask |
| AI / LLM | Gemini |
| RAG | Retrieval-Augmented Generation |
| Vector Database | ChromaDB |
| Database | SQLite |
| Voice AI | Faster-Whisper |
| PDF Processing | PyPDF / PDF extraction |
| Scheduling | APScheduler |
| Email | SMTP |
| Authentication | Flask Sessions + Werkzeug |
| Frontend | HTML, CSS, JavaScript, Jinja2 |
| Data Format | JSON |
| Environment Configuration | `.env` |
| Testing | Python test scripts |

---

# 🔄 Complete Data Flow

## Text Query

```text
User
 │
 ▼
Chat Interface
 │
 ▼
Flask API
 │
 ▼
User Profile + Language
 │
 ▼
RAG Retrieval
 │
 ▼
Gemini
 │
 ▼
Safety Processing
 │
 ▼
Response
 │
 ▼
SQLite Chat History
```

---

## Prescription

```text
Prescription
     │
     ▼
Upload
     │
     ▼
PDF Text Extraction
     │
     ▼
Gemini Vision / Analysis
     │
     ▼
Medicine Information
     │
     ▼
Medicine Cards
     │
     ▼
Chat History
```

---

## Voice

```text
Microphone
    │
    ▼
Audio File
    │
    ▼
Faster-Whisper
    │
    ▼
Transcribed Text
    │
    ▼
Healthcare AI
    │
    ▼
Response
```

---

## Medication Reminder

```text
Prescription / Manual Entry
            │
            ▼
     Medication Schedule
            │
            ▼
          SQLite
            │
            ▼
       APScheduler
            │
            ▼
       Time Match
            │
            ▼
      SMTP Notification
            │
            ▼
       User's Email
```

---

# 🚀 Getting Started

## Prerequisites

Install:

- Python 3.10+
- Git
- pip
- A Gemini API key
- SMTP credentials if email notifications are required

---

# 📥 Installation

### 1. Clone the repository

```bash
git clone https://github.com/VinitMakwana/Care-Companion.git
```

### 2. Enter the project

```bash
cd Care-Companion
```

### 3. Create a virtual environment

### Linux / macOS

```bash
python3 -m venv venv
source venv/bin/activate
```

### Windows

```powershell
python -m venv venv
venv\Scripts\activate
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

---

# 🔑 Environment Variables

Create a `.env` file in the project root.

Example:

```env
GEMINI_API_KEY=your_gemini_api_key

FLASK_SECRET_KEY=your_secure_secret_key

SMTP_EMAIL=your_email@example.com
SMTP_PASSWORD=your_app_password
SMTP_SERVER=smtp.gmail.com
SMTP_PORT=587
```

> **Never commit your `.env` file or API keys to GitHub.**

The application loads environment variables through `python-dotenv`, while the Flask secret key can be supplied through `FLASK_SECRET_KEY`.

---

# 📚 Add Healthcare Knowledge

Place trusted healthcare reference documents inside:

```text
knowledge_base/
```

Example:

```text
knowledge_base/
│
├── general/
│   ├── healthcare_basics.pdf
│   └── common_conditions.txt
│
├── medications/
│   ├── medication_guidelines.pdf
│   └── drug_information.md
│
└── first_aid/
    └── first_aid_guidelines.pdf
```

The application supports PDF, TXT, and Markdown documents and recursively scans subdirectories during RAG ingestion.

---

# ▶️ Run the Application

Start Flask:

```bash
python app.py
```

Then open:

```text
http://127.0.0.1:5000
```

---

# 🧪 Testing RAG

The repository includes:

```text
test_rag.py
```

which can be used to test the RAG functionality and knowledge-base retrieval.

Run:

```bash
python test_rag.py
```

---

# 🔌 Important API Endpoints

The application exposes APIs for its major healthcare functions.

| Endpoint | Purpose |
|---|---|
| `POST /api/chat` | Text-based AI conversation |
| `POST /api/upload-prescription` | Prescription analysis |
| `POST /api/upload-skin-image` | Image-based healthcare analysis |
| `POST /api/voice-to-text` | Voice transcription + AI response |
| `GET /api/scheduler` | Retrieve medication schedules |
| `POST /api/scheduler` | Create medication schedule |
| `DELETE /api/scheduler` | Deactivate medication schedule |
| `GET /api/smtp-status` | Check email configuration |
| `POST /api/test-email` | Test email notifications |

These endpoints are implemented in the Flask backend and protected by the application's authentication layer where appropriate.

---

# 📊 Database Design

Care Companion uses SQLite for persistent application data.

### Users

```text
users
├── id
├── username
├── email
├── password_hash
├── created_at
├── has_seen_disclaimer
└── has_completed_profile
```

### Profiles

```text
profiles
├── user_id
├── full_name
├── age
├── weight
├── height
├── email
├── contact
├── emergency_contact
├── preferred_language
├── health_conditions
└── allergies
```

### Medication Schedule

```text
medication_schedule
├── user_id
├── medicine_name
├── dosage
├── frequency
├── duration
├── instructions
├── precautions
├── times
├── start_date
├── end_date
└── is_active
```

### Chat

```text
chat_threads
     │
     └── chat_history
             ├── user messages
             ├── AI responses
             ├── image messages
             ├── voice messages
             └── prescription messages
```

The database schema is initialized directly by the Flask application.

---

# 🧠 AI Architecture

Care Companion follows a layered AI architecture:

```text
                   USER
                    │
                    ▼
             Input Detection
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
      Text         Voice        Image
       │            │            │
       │        Whisper       Gemini Vision
       │            │            │
       └────────────┼────────────┘
                    ▼
             Context Builder
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    Profile        RAG        Conversation
    Context       Context       History
       │            │            │
       └────────────┼────────────┘
                    ▼
                  Gemini
                    │
                    ▼
             Safety Guardrails
                    │
                    ▼
          Personalized Response
                    │
                    ▼
               SQLite
```

---

# 🛡️ Healthcare Safety

Care Companion is an **AI assistance system**, not a medical professional.

Users should not use the application as a substitute for:

- Doctors
- Emergency medical services
- Pharmacists
- Clinical diagnosis
- Professional medical advice

For serious or emergency symptoms, users should seek professional medical care immediately.

The application includes a disclaimer flow before users enter the main assistant experience.

---

# 🔒 Security Considerations

For a production deployment, additional security hardening should be implemented.

Recommended improvements include:

- Use strong production secrets
- HTTPS
- Secure cookies
- CSRF protection
- Rate limiting
- Input validation
- File-type validation
- Malware scanning for uploads
- Encryption for sensitive healthcare information
- Secure database configuration
- Proper access controls
- Audit logging
- Secrets management
- HIPAA/GDPR-compliant infrastructure where applicable

> This repository should currently be treated as an **educational/portfolio prototype**, not a production clinical system.

---

# ⚡ Performance Considerations

The current implementation is designed primarily for local development and demonstration.

For production-scale deployment, the architecture could evolve toward:

```text
             Load Balancer
                   │
          ┌────────┴────────┐
          ▼                 ▼
     Flask Worker       Flask Worker
          │                 │
          └────────┬────────┘
                   ▼
              Redis Queue
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
     RAG Worker  AI Worker  Scheduler
        │          │          │
        ▼          ▼          ▼
    Vector DB    LLM API    Notification
```

---

# 🔮 Future Enhancements

## 🤖 Advanced AI

- Healthcare-specific fine-tuned models
- Medical Named Entity Recognition
- Better prescription OCR
- Medication interaction checking
- Symptom extraction
- Medical entity linking
- Confidence scoring
- Source citations in responses

---

## 🧠 Advanced RAG

Future versions could implement:

- Hybrid search
- Semantic + keyword retrieval
- Re-ranking
- Metadata filtering
- Source prioritization
- Document versioning
- Retrieval evaluation
- Hallucination detection

---

## 💊 Smarter Medication Management

Potential additions:

- Drug interaction detection
- Missed-dose tracking
- Adherence analytics
- Refill reminders
- Medicine inventory
- Doctor/pharmacy integration
- Calendar synchronization
- WhatsApp notifications

---

## 🎙️ Advanced Voice AI

Potential architecture:

```text
Speech
  │
  ▼
Whisper
  │
  ▼
Healthcare LLM
  │
  ▼
Text Response
  │
  ▼
Text-to-Speech
  │
  ▼
Voice Response
```

This would enable a complete voice-to-voice healthcare assistant.

---

## 📱 Mobile Application

The backend can eventually support:

- Android application
- iOS application
- Progressive Web App
- Mobile notifications
- Wearable integration

---

## ☁️ Production Cloud Architecture

A future deployment could use:

```text
                         Cloud
                           │
                   ┌───────┴───────┐
                   │                │
                Frontend         Backend
                                   │
                     ┌─────────────┼─────────────┐
                     ▼             ▼             ▼
                  LLM API       Vector DB     SQL DB
                     │             │             │
                     ▼             ▼             ▼
                  Gemini        ChromaDB      PostgreSQL
                                   │
                                   ▼
                            Object Storage
```

Potential cloud technologies:

- AWS
- Microsoft Azure
- Google Cloud
- Docker
- Nginx
- Redis
- PostgreSQL

---

# 🧪 Example Use Cases

### 👤 General Healthcare Question

```text
User:
"What are common symptoms of dehydration?"

        ↓

Care Companion

        ↓

RAG Retrieval

        ↓

Healthcare Knowledge

        ↓

Grounded AI Response
```

### 💊 Prescription

```text
User uploads prescription

        ↓

AI analyzes image/document

        ↓

Medicine information extracted

        ↓

User reviews information

        ↓

Medication schedule created

        ↓

Reminder emails generated
```

### 🎙️ Voice

```text
User speaks

        ↓

Faster-Whisper

        ↓

Text transcription

        ↓

RAG + Gemini

        ↓

Healthcare response
```

---

# 📌 What This Project Demonstrates

Care Companion demonstrates practical implementation of:

- Generative AI
- Large Language Models
- Multimodal AI
- Retrieval-Augmented Generation
- Vector databases
- Prompt engineering
- Healthcare AI
- Voice AI
- Computer vision / image understanding
- PDF processing
- Natural language processing
- Flask backend development
- REST API development
- SQLite database design
- Authentication
- Session management
- Background scheduling
- SMTP integration
- Personalized AI systems
- Full-stack AI application development

---

# 👨‍💻 Author

## Vinit Makwana

**Aspiring Data Engineer | AI & Big Data Enthusiast | Full-Stack AI Developer**

### GitHub

[VinitMakwana](https://github.com/VinitMakwana?utm_source=chatgpt.com)

### Project

[Care Companion Repository](https://github.com/VinitMakwana/Care-Companion?utm_source=chatgpt.com)

---

# ⭐ Support

If you find this project interesting:

- ⭐ Star the repository
- 🍴 Fork the project
- 🐛 Report bugs
- 💡 Suggest improvements
- 🤝 Contribute

---

# ⚠️ Disclaimer

**Care Companion is an educational and experimental AI healthcare assistant.**

It does not provide professional medical diagnosis, treatment, or emergency medical services.

AI-generated responses may be inaccurate or incomplete. Always consult a qualified healthcare professional for medical decisions.

---

# 📜 License

If this project is intended for open-source distribution, add an appropriate `LICENSE` file to the repository.

---

## 🚀 Care Companion

```text
       ┌──────────────────────────────────────┐
       │          CARE COMPANION              │
       │                                      │
       │   Understand • Assist • Remind       │
       │                                      │
       │      AI-Powered Healthcare           │
       │           Assistance                 │
       └──────────────────────────────────────┘

 Text ───────┐
 Voice ──────┤
 Prescription├──► AI + RAG ──► Personalized Care
 Image ──────┤
 Profile ────┘                    │
                                  ▼
                         Medication Reminders
```

> **Care Companion — Making healthcare information more accessible, personalized, and intelligent.**
