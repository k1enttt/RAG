# GEMINI.md - Project Context

## Project Overview

This project consists of a collection of Python scripts that demonstrate various patterns for building Retrieval-Augmented Generation (RAG) and conversational AI applications using the LangChain and LangGraph libraries. It serves as a practical guide, with examples ranging from a basic RAG pipeline to more complex conversational agents with memory.

**Core Technologies:**
- **Language:** Python
- **Frameworks:** LangChain, LangGraph
- **LLM:** Google Gemini (`gemini-1.5-flash`)
- **Embeddings:** `OllamaEmbeddings` with the `bge-m3` model
- **Vector Store:** `InMemoryVectorStore`

**Architecture:**
The project is structured into three distinct, runnable scripts, each illustrating a different implementation pattern:
1.  `main.py`: A fundamental, non-conversational RAG pipeline.
2.  `rag-chains.py`: A conversational agent with memory, built using a custom-defined LangGraph graph.
3.  `rag-agents.py`: A conversational agent with memory, built using the high-level `create_react_agent` constructor from LangGraph.

All scripts currently use a hardcoded Vietnamese text document as the data source for the RAG process.

## Building and Running

### 1. Prerequisites

-   **Install Ollama:** This project requires a running Ollama instance to generate embeddings. Download and install it from [https://ollama.com/](https://ollama.com/).
-   **Pull Embedding Model:** After installing Ollama, ensure it is running and pull the required model:
    ```bash
    ollama pull bge-m3
    ```

### 2. Setup and Installation

-   **Install Dependencies:** Install the necessary Python packages from `requirements.txt`.
    ```bash
    pip install -r requirements.txt
    ```
-   **Configure Environment:** Create a `.env` file in the project root and add your Google API key:
    ```
    GOOGLE_API_KEY="your_google_api_key_here"
    ```
    If the `.env` file is not found, the scripts will prompt for the key at runtime.

### 3. Running the Scripts

Each script is self-contained and can be executed directly to demonstrate its specific pattern:

-   **Basic RAG:**
    ```bash
    python main.py
    ```
-   **Conversational RAG (Custom Graph):**
    ```bash
    python rag-chains.py
    ```
-   **Conversational RAG (Pre-built Agent):**
    ```bash
    python rag-agents.py
    ```

## Development Conventions

-   **Code Style:** The code is procedural, with clear separation of concerns for setup, data loading, graph definition, and execution.
-   **Dependencies:** Python package dependencies are managed through the `requirements.txt` file.
-   **Data Source:** The project currently uses a hardcoded text document. The `WebBaseLoader` code is present but commented out, indicating a potential area for expansion to use live web content.
-   **Testing:** There are no automated tests in the project. Verification is done by running the scripts and observing the output.
