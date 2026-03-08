# 🚀 Enterprise GenAI Assistant

A production-ready, multi-user RAG-based AI assistant that I built and deployed on Railway cloud platform.

**Live Demo:** https://divine-comfort-production-1b7d.up.railway.app  
**Backend API:** https://enterprise-genai-assistant-production.up.railway.app

---

## 🧠 What I Built

I built a scalable conversational AI platform that supports:
- Secure JWT-based user authentication
- Session-based chat system with persistent history
- Document upload & RAG (Retrieval Augmented Generation)
- Conversational memory (last 5 turns)
- Token usage & cost tracking
- Smart auto-title generation using LLM

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI (Async) |
| Database | PostgreSQL |
| ORM | SQLAlchemy (Async) |
| Vector DB | FAISS |
| LLM | LangChain + Groq |
| Embeddings | HuggingFace Sentence Transformers |
| Frontend | Streamlit |
| Auth | JWT |
| Deployment | Railway + Docker |

---

## 📁 Project Structure

```
enterprise-genai-assistant/
├── app/
│   ├── api/              # Auth, Chat, Session routes
│   ├── core/             # Config, Security
│   ├── db/               # Database setup
│   ├── langchain_layer/  # LLM, Embeddings, RAG
│   ├── middleware/        # RBAC
│   ├── models/           # DB Models
│   └── main.py           # FastAPI app
├── frontend/
│   └── app.py            # Streamlit UI
├── Dockerfile.backend    # Backend Docker image
├── Dockerfile.frontend   # Frontend Docker image
├── init_tables.py        # DB table creation script
├── requirements.txt
└── .env.example
```

---

## ⚙️ Local Setup

### 1. Clone the repo
```bash
git clone https://github.com/Satendra017/enterprise-genai-assistant.git
cd enterprise-genai-assistant
```

### 2. Virtual environment banao
```bash
python -m venv .venv

# Windows:
.venv\Scripts\activate

# Mac/Linux:
source .venv/bin/activate
```

### 3. Dependencies install karo
```bash
pip install -r requirements.txt
```

### 4. PostgreSQL start karo (Docker)
```bash
docker run --name genai-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=genaidb \
  -p 5433:5432 \
  -d postgres:15
```

### 5. .env file banao
```bash
cp .env.example .env
```

`.env` mein yeh values daalo:
```env
DATABASE_URL=postgresql+asyncpg://admin:secret@localhost:5433/genaidb
JWT_SECRET=your_long_random_secret_key
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60
DEFAULT_LLM_PROVIDER=groq
OPENAI_API_KEY=sk-dummy
GROQ_API_KEY=your_groq_key
HF_EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
HF_API_KEY=your_hf_key
DAILY_TOKEN_LIMIT=100000
BACKEND_URL=http://localhost:8000
```

### 6. Database tables banao
```bash
python init_tables.py
```
Output: `✅ All tables created successfully!`

### 7. Run karo

**Terminal 1 — Backend:**
```bash
uvicorn app.main:app --reload --port 8000
```

**Terminal 2 — Frontend:**
```bash
streamlit run frontend/app.py
```

App open hoga: http://localhost:8501

---

## ☁️ Railway Deployment

Maine is project ko Railway pe deploy kiya hai — 3 alag services:

```
Railway Project
├── 🐘 PostgreSQL Service   → Database
├── ⚙️  Backend Service      → FastAPI
└── 🖥️  Frontend Service     → Streamlit
```

### Deployment Steps

**1. GitHub pe push karo**
```bash
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/YOUR_USERNAME/enterprise-genai-assistant.git
git branch -M main
git push -u origin main
```

**2. Railway pe PostgreSQL add karo**
- railway.app pe login karo
- New Project → Database → PostgreSQL
- Variables tab se `DATABASE_URL` copy karo
- `postgresql://` ko `postgresql+asyncpg://` se replace karo

**3. Backend Service deploy karo**
- Add Service → GitHub Repo
- Settings → Build → Dockerfile → `Dockerfile.backend`
- Variables mein saari env vars add karo
- Settings → Networking → Generate Domain

**4. Frontend Service deploy karo**
- Add Service → GitHub Repo (same repo)
- Settings → Build → Dockerfile → `Dockerfile.frontend`
- Start Command:
```
streamlit run frontend/app.py --server.address 0.0.0.0 --server.headless true
```
- Variables mein `BACKEND_URL` = backend ka Railway URL daalo
- Settings → Networking → Generate Domain

---

## 🔄 CI/CD Pipeline

Railway aur GitHub ka integration automatic CI/CD pipeline ki tarah kaam karta hai:

```
Code change karo (PyCharm)
        ↓
git add . && git commit && git push
        ↓
GitHub pe update
        ↓
Railway automatically detect karta hai
        ↓
Auto re-deploy ho jaata hai 🚀
```

---

## 🔑 API Keys

| Key | Website | Cost |
|-----|---------|------|
| GROQ_API_KEY | console.groq.com | FREE |
| HF_API_KEY | huggingface.co/settings/tokens | FREE |
| OPENAI_API_KEY | platform.openai.com | Paid |

---

## ⚠️ Common Issues & Fixes

| Error | Fix |
|-------|-----|
| `asyncpg` connection error | `postgresql://` → `postgresql+asyncpg://` |
| Docker image > 4GB | CPU-only torch use karo |
| `$PORT` not valid | `--server.port` argument hata do |
| Port 5432 conflict | Docker ko port 5433 pe chalao |
| Module not found | Project root se run karo |

---

## 👨‍💻 Author

**Raj Bhardwaj**  
GenAI & FastAPI Python Developer