# LangGraph FastAPI Integration - Intelligent RAG Agent

A sophisticated AI agent system that integrates FastAPI with LangChain/LangGraph to provide intelligent routing between Retrieval-Augmented Generation (RAG), web search, and direct answering capabilities. This project demonstrates advanced conversational AI with context-aware decision making.

---
# 🏗️ Architecture Overview
![architecture](./assets/advanced-rag-agent-graph.png)

```
User Query → Router → [RAG | Web Search | Direct Answer] → Response
```
---

## 🚀 Features
- **🧠 Intelligent Routing**: The system will do decision-making between RAG, web search, and direct answering automatically
- **📚 RAG Integration**: Advanced document search using ChromaDB vector store with contextual query reformulation
- **🌐 Web Search**: Real-time information retrieval using Tavily API
- **💬 Session Management**: Persistent conversation history across requests
- **📄 Document Management**: Upload, index, list, and delete documents
- **🔍 Contextual Query Processing**: Smart query reformulation for follow-up questions
- **📊 Comprehensive Logging**: Detailed logging for debugging and analysis and save into a log file
- **🏗️ Modular Architecture**: Clean separation of concerns with dedicated modules

---

### Core Components

1. **Router Node**: Analyzes queries and routes to appropriate handler
2. **RAG Node**: Searches document knowledge base with intelligent judging
3. **Web Search Node**: Performs real-time web searches
4. **Answer Node**: Synthesizes information into coherent responses

---
## 📋 Prerequisites

- Python 3.8+
- OpenAI API Key
- Tavily API Key (for web search functionality)
- Git

---

### 🛠️ Installation & Setup (local machine)

#### 1. Clone the Repository

```bash
git clone <repository-url>
cd rad-langchain-agent
```

#### 2. Install Dependencies

Using `uv` (recommended):
```bash
pip/pip3 install uv
uv pip install -r server/requirements.txt
```

Or using pip(pip3):
```bash
pip/pip3 install -r api/requirements.txt
```

#### 3. Environment Configuration
Rename .env.example file to .env, and fill out the empty strings

#### 4. Initialize the Application

The database and vector store will be created automatically on first run.

## 🚀 Running the Application

### Development Server

```bash
cd server
uvicorn main:app --reload --host 0.0.0.0 --port 8080
```

### Production Server

```bash
cd server
uvicorn main:app --host 0.0.0.0 --port 8080
```

### Production/Development Server (Docker)
```bash
docker pull jcheng919/rag-langchain-fastapi:v1.0.1
docker run --rm --name rag-langchain-agent \
-e OPENAI_API_KEY="" \
-e LANGCHAIN_TRACING_V2="true" \
-e LANGCHAIN_ENDPOINT="https://api.smith.langchain.com" \
-e LANGCHAIN_API_KEY="" \
-e LANGCHAIN_PROJECT="" \
-e TAVILY_API_KEY="" \
-p 8080:8080 \
rag-langchain-fastapi:v1.0.1
```

The API will be available at `http://localhost:8080`

---
## 📚 API Documentation

### Interactive API Docs

Once running, visit:
- **Swagger UI**: `http://localhost:8080/docs`
- **ReDoc**: `http://localhost:8080/redoc`

### 1. Chat Endpoint

**POST** `/chat`

The main conversational endpoint using the intelligent LangGraph agent.

**Request Body:**
```json
{
  "question": "What is the GreenGrow company?",
  "session_id": "user-123",
  "model": "gpt-4.1-mini"
}
```

**Response:**
```json
{
  "answer": "The GreenGrow company...",
  "session_id": "user-123",
  "model": "gpt-4.1-mini"
}
```

**Features:**
- Automatic query contextualization for follow-up questions
- Intelligent routing based on query type
- Session-based conversation history
- Multiple model support

### 2. Document Management

#### Upload Document
**POST** `/upload-doc`

Upload and index documents for RAG functionality.

```bash
curl -X POST "http://localhost:8080/upload-doc" \
  -F "file=@your_document.pdf"
```

**Supported Formats:** PDF, DOCX, HTML

#### List Documents
**GET** `/list-docs`

Retrieve all uploaded documents.

```bash
curl -X GET "http://localhost:8080/list-docs"
```

#### Delete Document
**POST** `/delete-doc`

Remove documents from the system.

```bash
curl -X POST "http://localhost:8080/delete-doc" \
  -H "Content-Type: application/json" \
  -d '{"file_id": 1}'
```



## 📁 Project Structure

```
LangGraph FastAPI Integration/
├── server/                       # Main application directory
│   ├── main.py                   # FastAPI application with endpoints
│   ├── langgraph_agent.py        # LangGraph agent definition
│   ├── nodes.py                  # Agent node implementations
│   ├── tools.py                  # RAG and web search tools
│   ├── pydantic_models.py        # API request/response models
│   ├── db_utils.py               # Database operations
│   ├── chroma_utils.py           # Chroma vector store operations
│   ├── langchain_utils.py        # LangChain utilities
│   ├── shared.py                 # Shared configurations and types
│   ├── utils.py                  # Utility functions
│   ├── requirements.txt          # Python dependencies
│   ├── app.log                   # Application logs
├── docs/                         # Sample documents for testing
│   ├── Company_GreenFieldsBioTech.docx
│   ├── Company_QuantumNextSystems.docx
│   ├── Company_TechWaveInnovations.docx
│   ├── GreenGrowInnovations_Company History.docx
│   └── GreenGrow'sEcoHarvestSystem_ARevolutioninFarming.pdf
└── README.md                     # This current file
```

## 🔄 Agent Workflow

### 1. Query Processing
- User submits question
- System contextualizes query using chat history
- Router analyzes and determines optimal path

### 2. Routing Decision
- **`end`**: Greetings/small talk → Direct response
- **`rag`**: Document queries → Knowledge base search
- **`answer`**: General questions → Direct AI response

### 3. Information Retrieval
- **RAG Node**: Searches uploaded documents
- **Web Search Node**: Retrieves current information
- **Intelligent Judging**: Determines if RAG results are sufficient

### 4. Response Generation
- Synthesizes information from multiple sources
- Generates coherent, contextual responses
- Maintains conversation history

## 💡 Usage Examples

### Example 1: Document Query
```json
{
  "question": "What is the GreenGrow System?",
  "session_id": "user-123"
}
```
**Flow**: `router` → `rag` → `answer`

### Example 2: Follow-up Question
```json
{
  "question": "When was it introduced?",
  "session_id": "user-123"
}
```
**Flow**: Contextualization → `router` → `rag` → `answer`

### Example 3: Current Events
```json
{
  "question": "What's the latest news about AI developments?",
  "session_id": "user-123"
}
```
**Flow**: `router` → `rag` → `web` → `answer`

### Example 4: Simple Question
```json
{
  "question": "What is the capital of France?",
  "session_id": "user-123"
}
```
**Flow**: `router` → `answer`

## 🗄️ Database Schema

### Chat History Table
Tracks all user interactions for conversation continuity.

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Primary key, auto-increment |
| `session_id` | TEXT | Conversation session identifier |
| `user_query` | TEXT | User's question |
| `gpt_response` | TEXT | AI model response |
| `model` | TEXT | AI model used |
| `created_at` | TIMESTAMP | Timestamp of interaction |

### Document Store Table
Manages uploaded documents for RAG functionality.

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Primary key, auto-increment |
| `filename` | TEXT | Document filename |
| `upload_timestamp` | TIMESTAMP | Upload timestamp |

## 🔧 Configuration

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `OPENAI_API_KEY` | OpenAI API key for LLM access | Yes |
| `TAVILY_API_KEY` | Tavily API key for web search | No* |

*Required for web search functionality

### Model Options

- `gpt-4.1`: Full GPT-4 model for complex queries
- `gpt-4.1-mini`: Optimized model for faster responses

## 🐛 Troubleshooting

### Common Issues

1. **Missing API Keys**
   ```
   Error: OpenAI API key not found
   Solution: Set OPENAI_API_KEY in .env file
   ```

2. **Tavily Search Errors**
   ```
   Error: Tavily API key required for web search
   Solution: Set TAVILY_API_KEY or disable web search
   ```

3. **Chroma Vector Store Issues**
   ```
   Error: Vector store not found
   Solution: Vector store is auto-created in ./chroma_db
   ```

4. **Database Errors**
   ```
   Error: Database connection failed
   Solution: SQLite database is auto-created
   ```

### Logs

Check `server/app.log` for detailed application logs and debugging information.

## 🔒 Security Considerations

- Store API keys in environment variables
- Validate file uploads (type and size)
- Implement rate limiting for production
- Use HTTPS in production environments
- Regular security updates for dependencies

## 🚀 Performance Optimization

- Use `gpt-4.1-mini` for faster responses
- Implement caching for frequent queries
- Optimize vector store queries
- Monitor memory usage with large document sets

**Happy coding! 🚀** 