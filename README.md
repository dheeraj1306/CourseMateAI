# 📘 CourseMateAI

**An AI study assistant that answers questions from your own PDFs (textbooks, notes, course material) using Retrieval-Augmented Generation (RAG).**

Upload a PDF, ask a question in plain English, and get an answer grounded *only* in that document. If the answer isn't in the document, the assistant says so instead of making something up.

<!-- Add a screenshot or GIF of the app here. Recruiters love visuals! -->
<!-- ![CourseMateAI Demo](assets/demo.gif) -->

---

## 🎯 Problem It Solves

Students spend a lot of time searching through long PDFs and textbooks for one specific concept. General chatbots can answer, but they often **hallucinate** or ignore the course material. CourseMateAI fixes this by retrieving the most relevant passages from *your* document and forcing the LLM to answer only from them.

---

## ✨ Features

- 📄 **PDF upload**: works with any text-based PDF
- 🔍 **Semantic search**: finds relevant passages by meaning, not just keywords
- 🧠 **Grounded answers**: the LLM is instructed to use only the retrieved context
- 🚫 **Hallucination control**: replies *"I could not find the answer in the document."* when the context has no answer
- 💾 **Persistent vector store**: embeddings are saved locally, so the document does not need to be re-processed each time
- 🖥️ **Two interfaces**: a Streamlit web app and a command-line chat

---

## 🏗️ How It Works (RAG Pipeline)

```
PDF  →  Load  →  Split into chunks  →  Embed  →  Store in ChromaDB
                                                        │
User question  →  Embed  →  Retrieve top relevant chunks (MMR)
                                                        │
                       Context + Question  →  Mistral LLM  →  Answer
```

1. **Load**: `PyPDFLoader` reads the PDF page by page.
2. **Chunk**: `RecursiveCharacterTextSplitter` splits text into chunks of 1000 characters with 200 overlap, so context isn't lost at chunk boundaries.
3. **Embed**: each chunk is converted to a vector using the HuggingFace model `sentence-transformers/all-MiniLM-L6-v2`.
4. **Store**: vectors are saved in a local **ChromaDB** database.
5. **Retrieve**: for each question, the retriever uses **MMR (Maximal Marginal Relevance)** to fetch 4 chunks (from 10 candidates). MMR balances relevance with diversity, so the chunks are not near-duplicates.
6. **Generate**: the chunks and the question go into a strict prompt, and **Mistral (`open-mistral-7b`)** writes the answer.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Language | Python |
| Framework | LangChain |
| LLM | Mistral AI (`open-mistral-7b`) |
| Embeddings | HuggingFace `all-MiniLM-L6-v2` |
| Vector Database | ChromaDB |
| UI | Streamlit |
| Config | python-dotenv |

---

## 📁 Project Structure

```
CourseMateAI/
├── app.py              # Streamlit web app (upload PDF, build DB, ask questions)
├── createDatabase.py   # Script to build the vector DB from a PDF (CLI workflow)
├── main.py             # Command-line chat that queries the vector DB
├── requirements.txt    # Python dependencies
└── .gitignore
```

---

## 🚀 Getting Started

### 1. Clone the repository
```bash
git clone https://github.com/dheeraj1306/CourseMateAI.git
cd CourseMateAI
```

### 2. Create a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Add your API key
Create a `.env` file in the project root:
```
MISTRAL_API_KEY=your_mistral_api_key_here
```
You can get a key from the [Mistral AI console](https://console.mistral.ai/).

### 5. Run the app

**Option A: Web app (recommended)**
```bash
streamlit run app.py
```
Then upload a PDF, click **Create Vector Database**, and start asking questions.

**Option B: Command line**
```bash
python createDatabase.py   # builds the vector DB from your PDF
python main.py             # chat in the terminal (type 0 to exit)
```
> Note: update the PDF path inside `createDatabase.py` to point to your own file.

---

## 💡 Example

**Question:** *What is backpropagation?*
**Answer:** A response based only on the passages retrieved from your uploaded PDF.

**Question:** *Who won the 2018 World Cup?* (not in the PDF)
**Answer:** *"I could not find the answer in the document."*

---

## 🔮 Future Improvements

- [ ] Show source page numbers with each answer (citations)
- [ ] Support multiple PDFs and other formats (DOCX, TXT)
- [ ] Add chat history / conversational memory
- [ ] Deploy online (Streamlit Community Cloud / Hugging Face Spaces)
- [ ] Generate summaries, flashcards and quizzes from the document

---

## 👤 Author

**Dheeraj**
GitHub: [@dheeraj1306](https://github.com/dheeraj1306)
<!-- Add your LinkedIn and email here -->

---

⭐ If you found this project useful, consider giving it a star!
