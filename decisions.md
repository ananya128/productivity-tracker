# DESIGN DECISIONS

This document outlines the rationale behind the major technical decisions made while building the Productivity Tracker App using React, FastAPI, and GenAI. The goal was to design a modular, scalable, and intuitive system for daily task logging, visualization, GenAI-powered summaries, and vector-based historical search.

## 🔷 Frontend (React + Vite + TailwindCSS)

### 🗂 TaskList Tab Logic

- Provides Today, Upcoming, Backlog, and Completed task views using Shadcn Tabs.
- Supports CRUD operations on tasks: add, edit, delete, complete.
- Focus levels are color-coded and editable inline.

### 📈 Challenges Section

- Weekly tasks are filtered and parsed by date.
- Tasks with focusLevel = high are tracked across days for streaks.
- Completed tasks contribute to the 20-task goal.
- Focus-weighted minutes are used to compute 8-hour focus goal progress.
- The most productive day is calculated using task frequency.
- Longest task duration is computed from time values.

### 📊 Productivity Graph

- Tasks are mapped to dayjs dates and assigned focus scores (low=3, med=6, high=9).
- Buckets for each of the last 14 days accumulate tasks, minutes, and focus scores.
- Final chart series includes tasks, hours, and average focus level.
- recharts was chosen for interactive, responsive visualization.
- LocalStorage is used for persistent task storage.
- Sample JSON is loaded on first use to simulate realistic data.
- tasksUpdated event ensures all visual components re-render on task state change.

## 🔶 Backend (FastAPI + GenAI + Vector Search)
### 🧠 Weekly Summary Logic (Unified View)

- User selects a date → app calculates a 7-day range and filters completed tasks.
- It computes total tasks, total hours, average focus, and the most productive day.
- This metadata is sent to the /generate-summary backend endpoint.
- Backend formats the data into a carefully engineered prompt and sends it to GPT.
- GPT returns a Markdown summary rendered on the frontend with ReactMarkdown.

### 🔍 Historical Search Flow (Vector + Filter Hybrid)

- User enters a natural language query (e.g., "weeks where Tuesday was most productive").
- This query is embedded using the text-embedding-3-large model via OpenAI.
- A similarity search is performed over pre-embedded weekly summaries stored in ChromaDB.
- At the same time, metadata filters are applied (e.g., productive_day == Tuesday, average_focus < 5) for structured constraints.
- LangChain combines both filters to retrieve highly relevant summaries.
- Matching summaries are returned to the frontend, showing week ranges and metadata.
- This hybrid approach improves retrieval accuracy significantly compared to vector search alone.

### 🧠 GenAI Summary API (FastAPI)

- FastAPI was selected for its speed, asynchronous I/O, and auto-generated docs using OpenAPI.
- The /generate-summary endpoint receives weekly task metadata from the frontend and formats it into a structured prompt.

### 🧾 Prompt Engineering Decisions

- Multiple iterations were required to tune the prompt to avoid hallucinations, vague suggestions, and over-generic summaries.
- Instructions were explicitly added to:
   - Stick to a 3-part summary (Metrics, Trends, Suggestions)
   - Use a light, quirky tone
   - Avoid motivational clichés and instead be helpful and practical

Prompt structure was locked after validating results across different sample weeks.

### 🧠 LLM Filter Extraction Prompt Design

- A separate prompt was engineered for structured filter extraction from natural language queries.
- Initially, the LLM often hallucinated field names (e.g., inventing "coding_hours") or inserted non-standard values (like "coding" as a focus_tag).
  To fix this, the prompt was constrained using:
  - Explicitly listed allowed field names and allowed values (especially for enums like focus_tag).
  - A strict JSON format example that the LLM must match.
- Post-processing logic validates, filters, and normalizes the raw response using Python:
  - Removes invalid fields
  - Casts numeric values appropriately
  - Maps common synonyms (e.g., tasks_completed → task_count)

This dramatically reduced hallucinations and improved filter accuracy and interpretability.

### 🤖 OpenAI Model Selection

- Initially used OpenAI’s default embedding endpoint, but results were poor — similar queries returned irrelevant summaries.
- Switched to text-embedding-3-large model, which improved semantic clustering and similarity accuracy significantly.
- GPT model used for summary generation: gpt-3.5-turbo, chosen for its speed, cost-efficiency, and reliability.

### 🧩 Filtering Logic for Search

- Vector similarity alone was insufficient: queries like "weeks with low focus" returned unrelated weeks.
- Added structured metadata extraction (e.g., task count, average focus, productive day) using regex.
- To interpret user queries more flexibly, we call the LLM filter extraction module (see: extract_filters_with_llm) which parses natural language into structured filter objects.
- Implemented hybrid filtering: combines cosine similarity with rule-based filters (e.g., focus < 5, task count > 25).

This boosted precision for user queries and allowed natural language + logic-based hybrid search.

### 📦 Vector Store with LangChain + ChromaDB

- LangChain chosen to simplify document splitting, embedding, and retrieval workflows.
- ChromaDB was selected for local vector storage due to its fast setup, in-memory speed, and ease of experimentation.
- populate_vector_store.py is used to clean-slate and regenerate the store from weekly summaries.



