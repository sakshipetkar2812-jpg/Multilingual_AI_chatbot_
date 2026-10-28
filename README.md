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
```text
upl-academic-chatbot/
│
├── backend.py
├── data/
│   ├── Full_multilingual_dataset.json
│   └── upl_data.json
│
├── index.html
│
├── embeddings.npz
├── struct.index
├── unstruct.index
│
├── notebooks/
│   └── upl_final.ipynb
│
├── evaluation/
│   └── upl_chatbot_full_evaluation.xlsx
│
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```


## ⚙️ How It Works


## 🔄 Chatbot Workflow Architecture

The multilingual AI chatbot processes user queries through the following pipeline:

### 1. User Query
- Accepts queries in English, Hindi, Gujarati, Hinglish, and Guj-English.

### 2. Language Detection
- Identifies the language of the user's query.
- Helps generate responses in the detected language.

### 3. Query Expansion
- Expands short or ambiguous queries into meaningful search terms.
- Example: `"course"` → relevant full course names and related terms.

### 4. Hybrid Retrieval (FAISS)
Retrieves relevant information from two knowledge sources:

- **Structured QA Retrieval:** Searches structured question-answer chunks using `struct.index`.
- **Unstructured Website Retrieval:** Searches website content chunks using `unstruct.index`.

### 5. Smart Routing
Selects the appropriate response strategy based on retrieval confidence and query type:

- **High Confidence:** Uses the structured answer directly.
- **List Queries:** Activates list mode for queries about courses, programs, and other multiple-item information.
- **Low Confidence:** Combines structured and unstructured retrieval results for better context.

### 6. Context Building
- Combines the most relevant retrieved information.
- Limits the context to a maximum of **5,000 characters** before sending it to the LLM.

### 7. LLM Response Generation
- Uses **Groq API with LLaMA 3.3 70B** to generate responses.
- Injects language-specific instructions into the system prompt.
- Generates answers grounded in the retrieved context.

### 8. Multilingual Response
- Returns the final answer in the detected language.
- Provides a conversational experience for users asking academic queries in supported languages.

---

## 📊 Workflow Diagram

```text
User Query (Any Supported Language)
              |
              v
       Language Detection
              |
              v
         Query Expansion
              |
              v
       Hybrid Retrieval (FAISS)
          /           \
         v             v
 Structured QA     Website Chunks
  struct.index     unstruct.index
          \           /
           v         v
           Smart Routing
                 |
        +--------+--------+
        |        |        |
        v        v        v
     High     List      Low
  Confidence   Mode   Confidence
        |        |        |
        +--------+--------+
                 |
                 v
          Context Building
         (Maximum 5,000 Chars)
                 |
                 v
       Groq — LLaMA 3.3 70B
        (Language Instructions)
                 |
                 v
     Response in Detected Language
```


### Embedding Strategy

- Model: `intfloat/multilingual-e5-large`
- Prefix: `"passage: "` for documents, `"query: "` for search
- Similarity: FAISS `IndexFlatIP` (cosine similarity, not L2 distance)
- Cache: Saved to `embeddings.npz` → reloaded on next startup (no re-computation)

---

## 🚀 Setup Instructions

### Prerequisites

- Python 3.10+ 
- A free [Groq API key](https://console.groq.com) (sign up → API Keys → Create)

### Step 1 — Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/upl-academic-chatbot.git
cd upl-academic-chatbot
```

### Step 2 — Create Virtual Environment

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate
```

### Step 3 — Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 4 — Add Your API Key

```bash
cp .env.example .env
```

Open `.env` and add your Groq key:GROQ_API_KEY=your_groq_api_key_here


### Step 5 — Start the Backend

```bash
uvicorn backend:app --reload --port 8000
```

First run will take **3–5 minutes** to generate embeddings.  
After that it loads from cache in **~10 seconds**.

### Step 6 — Open the Frontend

Simply open `index.html` in your browser. That's it!

> ⚠️ Make sure `API_URL` in `index.html` is set to `http://127.0.0.1:8000/chat`

---

## 📡 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/chat` | Send a query, get AI answer |
| POST | `/reset` | Clear conversation history |
| GET | `/health` | Check if server is running |
| GET | `/docs` | Auto-generated Swagger UI (FastAPI) |

### Example Request

```bash
curl -X POST http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"query": "What courses are available?"}'
```

### Example Response

```json
{
  "query": "What courses are available?",
  "detected_lang": "English",
  "strategy": "list_mode",
  "confidence": "87.43%",
  "answer": "UPL University offers the following programs:\n1. BE Computer Engineering\n2. BE Civil Engineering\n3. BSc Microbiology\n4. MSc Chemistry\n...",
  "top_sources": ["structured", "unstructured"]
}
```

---

## 📊 Dataset

Two datasets power this chatbot:

| File | Type | Content |
|---|---|---|
| `Full_multilingual_dataset.json` | Structured QA | Question-Answer pairs in English, Hindi, Gujarati |
| `upl_data.json` | Unstructured | UPL University website content (scraped + cleaned) |

Both datasets are chunked using `RecursiveCharacterTextSplitter`:
- Chunk size: **600 characters**
- Overlap: **120 characters**

---

## 📈 Evaluation Results

Full evaluation available in `evaluation/upl_chatbot_full_evaluation.xlsx`

| Category | Questions Tested | Correct Answers |
|---|---|---|
| Admissions | 15 | 13 |
| Courses & Programs | 12 | 11 |
| Fees | 10 | 9 |
| Hostel | 8 | 7 |
| Placement | 8 | 7 |
| Hindi queries | 10 | 9 |
| Gujarati queries | 10 | 8 |
| **Overall** | **73** | **64** |

> **Overall Accuracy: ~87.7%** — evaluated on manually curated test questions.

---

## ⚠️ Known Limitations

- Embeddings file (`embeddings.npz`) is large and excluded from git — must be generated locally on first run
- No real-time data — answers based on dataset at time of training
- Conversation memory limited to last 2 exchanges (by design, to keep prompts short)
- Groq free tier has rate limits — may slow down under heavy load

---

## 🔮 Future Scope

- [ ] Deploy backend on Render / Railway (free hosting)
- [ ] Add voice input using Web Speech API
- [ ] Connect to live university database for real-time fee/seat data
- [ ] Add WhatsApp bot integration
- [ ] Fine-tune a smaller model on university data for offline use

---

## 👩‍💻 Author

**Sakshi**  
B.E. Computer Engineering  
📧 sakshipetkar2812@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/sakshi-petkar-1027352b0/)  
🐙 [GitHub]()

---

## 📄 License

This project is for academic purposes.  
UPL University data is used with educational intent only.
