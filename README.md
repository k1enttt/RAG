# Agentic RAG - LangChain Examples

## Tổng quan

Dự án này là một tập hợp các script Python minh họa các phương pháp triển khai RAG (Retrieval-Augmented Generation) và AI có khả năng trò chuyện (conversational AI) bằng cách sử dụng các thư viện LangChain và LangGraph. Các ví dụ được xây dựng dựa trên các hướng dẫn chính thức của LangChain, thể hiện các cách tiếp cận khác nhau từ cơ bản đến nâng cao.

Mục tiêu của dự án là cung cấp các ví dụ rõ ràng, dễ hiểu về:
-   Quy trình RAG cơ bản.
-   Xây dựng chatbot hỏi đáp có trí nhớ bằng cách sử dụng chains (chuỗi xử lý).
-   Xây dựng chatbot hỏi đáp có trí nhớ bằng cách sử dụng agents.

## Công nghệ sử dụng

-   **Ngôn ngữ**: Python 3.10
-   **Framework**: LangChain, LangGraph
-   **Model ngôn ngữ (LLM)**: Google Gemini (cụ thể là `gemini-1.5-flash`)
-   **Embeddings Model**: Ollama `bge-m3`
-   **Vector Store**: `InMemoryVectorStore` (Lưu trữ vector trong bộ nhớ)

## Cài đặt

1.  **Clone repository (nếu có):**
    ```bash
    git clone <your-repo-url>
    cd agentic-rag-2
    ```

2.  **Cài đặt Ollama và tải Embedding Model:**
    -   Chương trình này sử dụng `OllamaEmbeddings` để tạo embeddings. Bạn cần cài đặt Ollama trên máy của mình. Truy cập [https://ollama.com/](https://ollama.com/) để tải và cài đặt.
    -   Sau khi cài đặt xong, hãy chạy lệnh sau trong terminal để tải model `bge-m3` về:
        ```bash
        ollama pull bge-m3
        ```
    -   Đảm bảo rằng Ollama đang chạy dưới nền trước khi bạn thực thi các script Python.

3.  **Tạo môi trường ảo (khuyến khích):**
    ```bash
    python -m venv .venv
    source .venv/bin/activate  # Trên Windows: .venv\Scripts\activate
    ```

4.  **Cài đặt các thư viện Python cần thiết:**
    ```bash
    pip install -r requirements.txt
    ```

5.  **Cấu hình biến môi trường:**
    -   Tạo một file tên là `.env` ở thư mục gốc của dự án.
    -   Thêm API key của bạn vào file `.env` như sau:
        ```
        GOOGLE_API_KEY="your_google_api_key_here"
        ```
    -   Nếu bạn không tạo file `.env`, các script sẽ yêu cầu bạn nhập API key khi chạy.

## Mô tả các Scripts

### `main.py`

-   **Mục đích**: Minh họa một quy trình RAG cơ bản nhất.
-   **Dựa trên hướng dẫn**: [LangChain RAG Tutorial](https://python.langchain.com/docs/tutorials/rag/)
-   **Cách hoạt động**:
    1.  Script tải một tài liệu văn bản (trong trường hợp này là một bài báo tiếng Việt về chip Google Tensor).
    2.  Nó chia tài liệu thành các đoạn nhỏ (chunks) và tạo embeddings cho mỗi đoạn.
    3.  Các embeddings được lưu trữ trong một `InMemoryVectorStore`.
    4.  Khi có một câu hỏi, script sẽ tìm kiếm các đoạn văn bản liên quan nhất trong vector store.
    5.  Cuối cùng, nó gửi các đoạn văn bản đó cùng với câu hỏi đến LLM để tạo ra câu trả lời.
-   **Luồng xử lý**: Sử dụng `langgraph` để định nghĩa một chuỗi xử lý đơn giản: `retrieve` -> `generate`.

### `rag-chains.py`

-   **Mục đích**: Minh họa cách xây dựng một chatbot hỏi đáp có khả năng ghi nhớ lịch sử hội thoại bằng cách xây dựng một đồ thị (graph) tùy chỉnh với `langgraph`.
-   **Dựa trên hướng dẫn**: [LangChain QA with Chat History](https://python.langchain.com/docs/tutorials/qa_chat_history/)
-   **Cách hoạt động**:
    1.  Sử dụng cùng một cơ chế RAG như `main.py` để truy xuất thông tin.
    2.  Xây dựng một đồ thị xử lý phức tạp hơn với các node: `query_or_respond` (quyết định hành động), `tools` (thực thi công cụ truy xuất), và `generate` (tạo câu trả lời).
    3.  Sử dụng `MemorySaver` để lưu lại lịch sử của cuộc trò chuyện, cho phép bot trả lời các câu hỏi tiếp theo trong cùng một ngữ cảnh.
    4.  File này cho thấy cách kiểm soát chi tiết luồng hoạt động của một agent.

### `rag-agents.py`

-   **Mục đích**: Minh họa cách tạo nhanh một chatbot hỏi đáp có trí nhớ bằng cách sử dụng `create_react_agent` được xây dựng sẵn trong `langgraph`.
-   **Dựa trên hướng dẫn**: [LangChain QA with Chat History](https://python.langchain.com/docs/tutorials/qa_chat_history/)
-   **Cách hoạt động**:
    1.  Tương tự như `rag-chains.py`, nó cũng là một chatbot có khả năng truy xuất thông tin và ghi nhớ ngữ cảnh.
    2.  Tuy nhiên, thay vì xây dựng đồ thị thủ công, nó sử dụng hàm `create_react_agent`. Đây là một abstraction cấp cao giúp đơn giản hóa việc tạo ra một agent theo kiến trúc ReAct (Reasoning and Acting).
    3.  Đây là một ví dụ về cách nhanh chóng tạo ra một agent mạnh mẽ mà không cần đi sâu vào chi tiết của việc xây dựng graph.

## Cách chạy

Bạn có thể chạy từng script một cách độc lập để xem kết quả của từng phương pháp.

```bash
# Chạy RAG cơ bản
python main.py

# Chạy chatbot hỏi đáp với graph tùy chỉnh
python rag-chains.py

# Chạy chatbot hỏi đáp với agent tạo sẵn
python rag-agents.py
```