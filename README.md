# 🧠 Productivity Tracker App (React + FastAPI + GenAI)

An AI-powered productivity tracking app that helps you log daily tasks, visualize productivity trends, generate weekly summaries using GenAI, and perform intelligent historical searches with vector similarity and metadata filtering.

---

## 🚀 Features

### ✏️ Daily Logging
- Add tasks with fields: task name, time spent, and focus level (1–10)
- View weekly task inputs

### 📊 Visual Insights
- Line showing focus and completion trends
- Highlights productive days and patterns

### 🔮 GenAI Weekly Summaries
- Auto-generates a 3-part summary:
  1. Weekly Metrics  
  2. Trends & Observations  
  3. Actionable Suggestions

### 🔍 Historical Search (GenAI Task Part 2)
- Search past weeks with natural language queries
- Uses vector similarity + structured filters (e.g., focus level, task count, productive day)
- Example Queries:
  - "Show weeks where my focus was below 5"
  - "Find weeks when Tuesday was my best day"

---

## 📦 Project Structure
```bash
productivity-tracker/
├── node_modules/
├── public/
│   └── productivityTasks_two_weeks.json         # Sample data for weekly summaries
├── src/
│   ├── backend/
│   │   ├── main.py                              # FastAPI for GenAI summary
│   │   ├── search_api.py                        # FastAPI for historical search
│   │   ├── weekly_summaries.json                # Sample summaries for vector store
│   │   ├── populate_vector_store.py             # Populates Chroma vector store
│   │   ├── filters.py                           # Utility file
│   │   ├── search.py                            # Utility file
│   │   └── .env                                 # ✅ Add your OpenAI API key here
│   ├── components/                              # Frontend components
│   ├── hooks/
│   ├── lib/
│   ├── pages/
│   ├── App.tsx                                  # Frontend entry
│   ├── main.tsx                                 # Frontend entry
│   └── index.css                                # Frontend styling
├── README.md
├── DECISIONS.md                                 # Justification of tools used
├── package.json
├── vite.config.ts
```

---

## 🧠 .env Setup

Inside `src/backend/`, create a `.env` file with the following content:
OPENAI_API_KEY=sk-xxxxx

``

---

## 🧪 Local Setup

### 1. Clone the repo

```bash
git clone https://github.com/ananya128/productivity-tracker.git
cd productivity-tracker
```

### 2. Frontend Setup
```bash
npm install
npm run dev
```
If Vite shows missing plugin errors:
```bash
npm install dayjs
```

### 3. Backend Setup
```bash
cd src/backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```
### 4.Build Vector Store
```bash
python3 populate_vector_store.py
```
### 5.Start APIs
#### Run Weekly Summary API (FastAPI):
```bash
uvicorn main:app --reload
```
Accessible at: http://localhost:8000/generate-summary

#### Run Search API (for historical search):
```bash
uvicorn search_api:app --reload --port 8001
```
Accessible at: http://localhost:8001/search

## Sample Queries to Try
	•	Weeks with focus level below 5
	•	Find weeks when I completed over 25 tasks
	•	Show weeks where most productive day was Tuesday
	•	Low focus but high task output



## Sample Data
	•	productivityTasks_two_weeks.json — used to test the weekly summary (frontend integration)
	•	weekly_summaries.json — ingested by populate_vector_store.py for historical vector search



## DECISIONS.md

See the DECISIONS.md file for:
	•	Library choices (e.g., LangChain, Chroma)
	•	Embedding model used (text-embedding-3-large)
	•	Justification for separating summary + search APIs
