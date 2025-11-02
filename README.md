# RAG Q&A 

A modern document question-answering system powered by Google's Gemini AI, built with FastAPI, LangChain, and FAISS vector store. Upload documents (PDF, DOCX, TXT) and ask questions to get AI-generated answers with source citations.

![RAG Q&A Interface](screenshot.png)

## Features

-  **Multi-format Support**: Upload PDF, DOCX, or TXT files
-  **Smart Retrieval**: Uses FAISS vector store for efficient similarity search
- 🤖 **Gemini AI**: Powered by Google's Gemini 1.5 Flash model
-  **Source Citations**: Get answers with relevant document snippets
-  **Modern UI**: Clean, dark-themed interface with drag-and-drop support
-  **Fast Processing**: Efficient document chunking and embedding generation

## Tech Stack

### Backend
- **FastAPI**: High-performance Python web framework
- **LangChain**: Framework for building LLM applications
- **FAISS**: Vector similarity search library
- **Google Gemini AI**: LLM for question answering and embeddings
- **PyPDF, docx2txt**: Document parsing libraries

### Frontend
- **Vanilla JavaScript**: No framework dependencies
- **Modern CSS**: Custom dark theme with gradients
- **Responsive Design**: Works on desktop and mobile

## Project Structure

```
.
├── client/
│   ├── index.html          # Main HTML page
│   ├── app.js              # Frontend JavaScript logic
│   └── styles.css          # CSS styling
├── server/
│   ├── __init__.py         # Python package marker
│   ├── main.py             # FastAPI server and routes
│   └── rag.py              # RAG engine implementation
├── requirements.txt        # Python dependencies
└── .env                    # Environment variables (not committed)
```

## Installation

### Prerequisites
- Python 3.8 or higher
- Google API Key for Gemini AI

### Setup Steps

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd rag-qa-gemini
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up environment variables**
   
   Create a `.env` file in the root directory:
   ```env
   GOOGLE_API_KEY=your_google_api_key_here
   ```
   
   Get your API key from [Google AI Studio](https://makersuite.google.com/app/apikey)

5. **Create server package**
   
   Ensure `server/__init__.py` exists (can be empty):
   ```bash
   touch server/__init__.py
   ```

## Usage

### Starting the Server

Run the FastAPI server:
```bash
uvicorn server.main:app --reload --port 8000
```

The application will be available at `http://localhost:8000`

### Using the Application

1. **Upload a Document**
   - Click the dropzone or drag & drop a file (PDF, DOCX, or TXT)
   - Click "Upload & Build Index" button
   - Wait for confirmation message showing chunks created

2. **Ask Questions**
   - Type your question in the input field
   - Click "Get Answer" button
   - View the AI-generated answer and source snippets

## API Endpoints

### `GET /`
Returns the main HTML page

### `POST /api/upload`
Upload and index a document

**Request:**
- Content-Type: `multipart/form-data`
- Body: File upload

**Response:**
```json
{
  "status": "ok",
  "message": "Index built successfully",
  "chunks": 42,
  "file": "document.pdf"
}
```

### `POST /api/ask`
Ask a question about the uploaded document

**Request:**
```json
{
  "question": "What is the main topic?"
}
```

**Response:**
```json
{
  "status": "ok",
  "answer": "The main topic is...",
  "sources": [
    {
      "id": 1,
      "page": 5,
      "metadata": {...},
      "snippet": "Relevant text from document..."
    }
  ]
}
```

## How It Works

1. **Document Upload**: Files are parsed using appropriate loaders (PyPDF, Docx2txt, TextLoader)

2. **Text Chunking**: Documents are split into 500-character chunks with 50-character overlap using RecursiveCharacterTextSplitter

3. **Embedding Generation**: Text chunks are converted to vector embeddings using Google's embedding-001 model

4. **Vector Storage**: Embeddings are stored in FAISS index for efficient similarity search

5. **Question Answering**: 
   - User question is embedded
   - Similar chunks are retrieved from FAISS
   - Gemini 1.5 Flash generates answer using retrieved context
   - Source documents are returned with citations

## Configuration

### Chunk Size
Modify in `server/rag.py`:
```python
splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,      # Adjust chunk size
    chunk_overlap=50     # Adjust overlap
)
```

### Model Selection
Change Gemini model in `server/rag.py`:
```python
self.llm = ChatGoogleGenerativeAI(
    model="gemini-1.5-flash",  # or gemini-1.5-pro
    google_api_key=os.getenv("GOOGLE_API_KEY")
)
```

### Retriever Parameters
Adjust retrieval settings:
```python
self.retriever = self.vectorstore.as_retriever(
    search_kwargs={"k": 4}  # Number of chunks to retrieve
)
```

## Dependencies

Key packages (see `requirements.txt` for complete list):
- `fastapi==0.115.5` - Web framework
- `langchain` - LLM application framework
- `langchain-google-genai` - Gemini integration
- `faiss-cpu==1.8.0.post1` - Vector similarity search
- `pypdf==4.3.1` - PDF parsing
- `docx2txt==0.8` - DOCX parsing

## Troubleshooting

### Missing API Key
**Error:** `Missing GOOGLE_API_KEY in environment`
**Solution:** Ensure `.env` file exists with valid `GOOGLE_API_KEY`

### Import Errors
**Error:** `No module named 'server'`
**Solution:** Make sure `server/__init__.py` exists

### File Upload Fails
**Error:** `Only .pdf, .txt, .docx are supported`
**Solution:** Use supported file formats only

### Empty Answers
**Error:** `No document uploaded yet`
**Solution:** Upload and index a document before asking questions

## Future Enhancements

- [ ] Support for multiple document uploads
- [ ] Conversation history and context
- [ ] Export answers to PDF/DOCX
- [ ] Advanced search filters
- [ ] User authentication
- [ ] Cloud storage integration
- [ ] Streaming responses
- [ ] Multi-language support

 

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Acknowledgments

- Google Gemini AI for powerful language models
- LangChain for the RAG framework
- FastAPI for the excellent web framework
- FAISS for efficient vector search

 
