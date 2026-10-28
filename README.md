# 🎓 UPL University Academic Chatbot

> An intelligent multilingual chatbot for **UPL University of Sustainable Technology, Ankleshwar, Gujarat** — built to answer student queries about admissions, courses, fees, hostel, placements and more.

---

## 📌 Table of Contents

- [About the Project](#about-the-project)
- [Live Demo](#live-demo)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Setup Instructions](#setup-instructions)
- [API Endpoints](#api-endpoints)
- [Dataset](#dataset)
- [Evaluation Results](#evaluation-results)
- [Known Limitations](#known-limitations)
- [Future Scope](#future-scope)
- [Author](#author)

---

## 🧠 About the Project

UPL University receives hundreds of repetitive student queries every day — about admission procedures, fee structures, course eligibility, hostel facilities, and placement records. Staff spend significant time answering the same questions repeatedly.

This project builds an AI-powered chatbot that:
- Answers queries **automatically in 3 languages** (English, Hindi, Gujarati) + mixed scripts (Hinglish, Guj-English)
- Uses **RAG (Retrieval Augmented Generation)** to ground answers in real university data
- Runs **entirely free** using Groq's free API (LLaMA 3.3 70B model)

---

## 🎥 Live Demo

> Open `index.html` in your browser after starting the backend.

![Chatbot Screenshot](screenshots/chatbot_ui.png)

---

## ✨ Features

| Feature | Description |
|---|---|
| 🌐 Multilingual | English, Hindi, Gujarati, Hinglish, Guj-English |
| 🔍 Hybrid RAG | Searches both structured FAQ + unstructured website data |
| 🧩 Query Expansion | Automatically expands short queries for better search |
| 💬 Conversation Memory | Remembers last 2 exchanges for follow-up questions |
| ⚡ Fast Loading | Embeddings cached to disk — loads in seconds after first run |
| 🎯 Smart Routing | Detects list queries vs detail queries and responds differently |
| 🔄 FAISS IndexFlatIP | Uses cosine similarity (not L2 distance) for accurate retrieval |
| 🚫 Graceful Fallback | If no info found, gives university contact details |

---

## 🛠️ Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | HTML, CSS, JavaScript | Lightweight, no build step needed |
| Backend | Python FastAPI | Fast, async, auto-generates API docs |
| LLM | Groq API — LLaMA 3.3 70B | Free tier, very fast inference |
| Embeddings | `intfloat/multilingual-e5-large` | Best multilingual sentence embeddings |
| Vector Search | FAISS (IndexFlatIP) | Fast cosine similarity search |
| Text Splitting | LangChain RecursiveCharacterTextSplitter | Smart chunking with overlap |
| Language Detection | `langdetect` + custom regex | Handles Gujarati/Devanagari scripts |

---

## 📁 Project Structure
upl-academic-chatbot/
│
├── backend.py # Main FastAPI backend (RAG pipeline)
│
├── data/
│ ├── Full_multilingual_dataset.json # Structured QA pairs (EN/HI/GU)
│ └── upl_data.json # Unstructured university website data
│
├── index.html # Frontend chatbot UI (plain HTML/CSS/JS)
│
├── embeddings.npz # Cached embeddings (auto-generated, not in git)
├── struct.index # Cached FAISS index (auto-generated, not in git)
├── unstruct.index # Cached FAISS index (auto-generated, not in git)
│
├── notebooks/
│ └── upl_final.ipynb # Experiments and evaluation notebook
│
├── evaluation/
│ └── upl_chatbot_full_evaluation.xlsx # Full evaluation results
│
├── requirements.txt # Python dependencies
├── .env.example # API key template (copy to .env)
├── .gitignore # Excludes .env, cache files, large indexes
└── README.md


---

## ⚙️ How It Works

### RAG Pipeline

User Query (any language)
↓

Language Detection
(English / Hindi / Gujarati / Hinglish / Guj-English)
↓
Query Expansion
(“course” → expands to full course-name terms)
↓
Hybrid Retrieval (FAISS)
├── Search Structured QA chunks (struct.index)
└── Search Unstructured website chunks (unstruct.index)
↓
Smart Routing
├── If high confidence → use structured answer
├── If list query (courses, programs) → list mode
└── If low confidence → use unstructured + structured both
↓
Context Building (max 5000 chars)
↓
LLM Call (Groq — LLaMA 3.3 70B)
Language instruction injected in system prompt
↓
Response in detected language → User
