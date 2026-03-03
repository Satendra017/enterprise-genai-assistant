# 🚀 Enterprise GenAI Assistant — Deployment Guide

---

## PART 1: Local pe Run Karo

### Step 1 — Dependencies install karo

```bash
cd enterprise-genai-assistant-main

python -m venv .venv

# Mac/Linux:
source .venv/bin/activate

# Windows:
.venv\Scripts\activate

pip install -r requirements.txt
```

---

### Step 2 — PostgreSQL start karo (Docker se, easiest hai)

```bash
docker run --name genai-db \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -e POSTGRES_DB=genaidb \
  -p 5432:5432 \
  -d postgres:15
```

> Agar Docker nahi hai toh PostgreSQL directly install karo:
> https://www.postgresql.org/download/

---

### Step 3 — .env file banao

```bash
cp .env.example .env
```

Ab `.env` file kholo aur values fill karo:

```env
DATABASE_URL=postgresql+asyncpg://admin:secret@localhost:5432/genaidb
JWT_SECRET=koi_bhi_lamba_random_string_likho_yahan_32chars_min
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60

DEFAULT_LLM_PROVIDER=groq

OPENAI_API_KEY=sk-...
GROQ_API_KEY=gsk_...        # FREE: console.groq.com pe jaake banao

HF_EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
HF_API_KEY=hf_...           # FREE: huggingface.co/settings/tokens

DAILY_TOKEN_LIMIT=100000
BACKEND_URL=http://localhost:8000
```

---

### Step 4 — Database tables banao (SIRF PEHLI BAAR)

```bash
python init_tables.py
```

Output aana chahiye: `✅ All tables created successfully!`

---

### Step 5 — Backend run karo

```bash
uvicorn app.main:app --reload --port 8000
```

Browser mein check karo: http://localhost:8000
Response aana chahiye: `{"status": "Running"}`

---

### Step 6 — Frontend run karo (naya terminal)

```bash
streamlit run frontend/app.py
```

Browser mein khulega: http://localhost:8501

---

## PART 2: Railway pe Deploy Karo

### Step 1 — GitHub pe push karo

```bash
# GitHub pe nayi repository banao: github.com/new
# Naam daal do: enterprise-genai-assistant

cd enterprise-genai-assistant-main

git init
git add .
git commit -m "initial commit"

git remote add origin https://github.com/TERA_USERNAME/enterprise-genai-assistant.git
git branch -M main
git push -u origin main
```

---

### Step 2 — Railway account banao

1. Jao: **railway.app**
2. **"Login with GitHub"** se login karo
3. GitHub account connect karo

---

### Step 3 — PostgreSQL Database banao

1. Railway dashboard mein **"New Project"** click karo
2. **"Add Service"** → **"Database"** → **"PostgreSQL"** select karo
3. Database create hone ke baad **"Variables"** tab kholo
4. `DATABASE_URL` copy kar lo (format: `postgresql://user:pass@host:port/db`)
5. **IMPORTANT:** URL ke starting mein `postgresql://` ko `postgresql+asyncpg://` se replace karo

---

### Step 4 — Backend Service Deploy karo

1. Project mein **"Add Service"** → **"GitHub Repo"** click karo
2. Apna repo select karo
3. Service create hone ke baad **"Settings"** tab pe jao:
   - **Dockerfile Path:** `Dockerfile.backend`
   - **Start Command:** `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
4. **"Variables"** tab pe jao, yeh sab add karo:

```
DATABASE_URL          = postgresql+asyncpg://... (Step 3 wala)
JWT_SECRET            = koi_lamba_random_string
JWT_ALGORITHM         = HS256
ACCESS_TOKEN_EXPIRE_MINUTES = 60
DEFAULT_LLM_PROVIDER  = groq
OPENAI_API_KEY        = sk-...
GROQ_API_KEY          = gsk_...
HF_EMBEDDING_MODEL    = sentence-transformers/all-MiniLM-L6-v2
HF_API_KEY            = hf_...
DAILY_TOKEN_LIMIT     = 100000
BACKEND_URL           = (abhi blank rakho, baad mein update karenge)
```

5. Deploy hone do. **"Deployments"** tab mein logs dekho.
6. Deploy hone ke baad **"Settings"** → **"Networking"** → **"Generate Domain"** click karo
7. URL copy karo, e.g.: `https://enterprise-backend-abc123.railway.app`

---

### Step 5 — DB Tables banao (Railway pe ek baar)

Backend service ke **"Settings"** mein **"Deploy"** section mein:
- Temporarily Start Command change karo: `python init_tables.py`
- Deploy karo, logs mein ✅ dikhega
- Start Command wapas karo: `uvicorn app.main:app --host 0.0.0.0 --port $PORT`

---

### Step 6 — Frontend Service Deploy karo

1. Phir se **"Add Service"** → **"GitHub Repo"** → same repo
2. **"Settings"** tab:
   - **Dockerfile Path:** `Dockerfile.frontend`
   - **Start Command:** `streamlit run frontend/app.py --server.port $PORT --server.address 0.0.0.0 --server.headless true`
3. **"Variables"** tab mein sirf yeh add karo:

```
DATABASE_URL          = postgresql+asyncpg://... (same as backend)
JWT_SECRET            = same as backend
JWT_ALGORITHM         = HS256
ACCESS_TOKEN_EXPIRE_MINUTES = 60
DEFAULT_LLM_PROVIDER  = groq
OPENAI_API_KEY        = sk-...
GROQ_API_KEY          = gsk_...
HF_EMBEDDING_MODEL    = sentence-transformers/all-MiniLM-L6-v2
HF_API_KEY            = hf_...
DAILY_TOKEN_LIMIT     = 100000
BACKEND_URL           = https://enterprise-backend-abc123.railway.app  ← Step 4 wala URL
```

4. Deploy hone do.
5. Frontend ke liye bhi **"Generate Domain"** karo.
6. URL kholo — app ready! 🎉

---

### Step 7 — Backend mein BACKEND_URL update karo

1. Backend service → **"Variables"**
2. `BACKEND_URL` = apna backend railway URL daalo
3. Redeploy ho jaayega automatically.

---

## Quick Reference — API Keys kahan se lein?

| Key | Website | Cost |
|-----|---------|------|
| GROQ_API_KEY | console.groq.com | FREE |
| OPENAI_API_KEY | platform.openai.com | Paid |
| HF_API_KEY | huggingface.co/settings/tokens | FREE |

---

## Common Errors & Fix

| Error | Fix |
|-------|-----|
| `asyncpg` connection error | URL mein `postgresql://` → `postgresql+asyncpg://` karo |
| Tables not found | `python init_tables.py` run karo |
| CORS error frontend pe | Backend ke `main.py` mein frontend URL allow karo |
| HF model download slow | Pehli baar slow hoga, model cache hota hai |

---
