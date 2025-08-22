# Kế hoạch Triển khai Giao diện Gradio cho RAG Agent (Nâng cao)

Tài liệu này mô tả các bước cần thiết để xây dựng một giao diện web bằng Gradio, bao gồm các tính năng nâng cao như quản lý phiên người dùng riêng biệt, lưu trữ vector bền vững và streaming câu trả lời.

## Kế hoạch Tổng thể

1.  **Tái cấu trúc `rag-agents.py`**: Chuyển sang dùng `ChromaDB` để lưu trữ vector bền vững và cấu trúc lại file thành một module có thể import.
2.  **Tạo file `app.py`**: Xây dựng giao diện Gradio có khả năng quản lý phiên người dùng và hiển thị câu trả lờiแบบ streaming (hiệu ứng typing).
3.  **Cập nhật Dependencies**: Đảm bảo các thư viện cần thiết (`gradio`, `chromadb`) có trong `requirements.txt` và được cài đặt.
4.  **Chạy ứng dụng**: Khởi động server Gradio để tương tác với agent.

---

## Các bước thực hiện chi tiết

### Bước 1: Tái cấu trúc `rag-agents.py` (Thêm Persistence & Module hóa)

Mục tiêu của bước này là kép:
1.  Chuyển từ `InMemoryVectorStore` sang `ChromaDB` để dữ liệu vector được lưu trữ bền vững.
2.  Cấu trúc lại file để có thể `import` agent vào các file khác.

-   **Công việc**: Thay thế phần khởi tạo Vector Store và đặt phần chạy script cũ vào trong khối `if __name__ == "__main__":`.

    ```python
    # --- Thay đổi ở phần LangChain Model & Embeddings ---
    from langchain_community.vectorstores import Chroma # Thay vì InMemoryVectorStore
    # ...
    # Bỏ dòng vector_store = InMemoryVectorStore(embeddings)

    # --- Thay đổi ở phần Document Loading & Chunking ---
    # ... (sau khi có all_splits)
    vector_store = Chroma.from_documents(
        documents=all_splits,
        embedding=embeddings,
        persist_directory="./chroma_db_agents" # Dùng thư mục riêng cho agent
    )
    print("Đã tạo và lưu trữ vector store vào ./chroma_db_agents")

    # --- Toàn bộ phần định nghĩa agent_executor giữ nguyên ---
    # ...

    # --- Đặt phần chạy thử vào if __name__ == "__main__" ---
    if __name__ == "__main__":
        # Phần code chạy tương tác cũ sẽ nằm ở đây
        print("Chạy ở chế độ script độc lập...")
        config = {"configurable": {"thread_id": "test-thread"}}
        # ... (vòng lặp for cũ)
    ```

### Bước 2: Tạo `app.py` (Thêm Streaming & Quản lý Phiên)

File này sẽ tạo giao diện web, quản lý phiên người dùng và hiển thị câu trả lời theo kiểu "typing".

-   **Công việc**: Tạo file mới `app.py` với nội dung sau:

    ```python
    import gradio as gr
    import uuid
    from rag_agents import agent_executor

    print("Đã import agent thành công.")

    def chat_stream_func(message, history, session_state):
        """
        Hàm xử lý logic chat có hỗ trợ streaming và quản lý phiên.
        """
        # 1. Quản lý Session/Thread ID
        if session_state is None:
            session_state = {}
        thread_id = session_state.get("thread_id")
        if thread_id is None:
            thread_id = str(uuid.uuid4())
            session_state["thread_id"] = thread_id
            print(f"Phiên làm việc mới. Thread ID: {thread_id}")
        
        config = {"configurable": {"thread_id": thread_id}}
        
        # 2. Streaming câu trả lời
        response = ""
        stream = agent_executor.stream(
            {"messages": [{"role": "user", "content": message}]},
            stream_mode="values",
            config=config,
        )
        
        # Dùng 'yield' để trả về từng phần của câu trả lời
        for event in stream:
            response = event["messages"][-1].content
            yield response, session_state

    # Tạo giao diện Gradio
    iface = gr.ChatInterface(
        fn=chat_stream_func,
        additional_inputs=[gr.State(None)],
        additional_outputs=[gr.State()],
        title="Conversational RAG Agent (Streaming)",
        description="Hỏi bất cứ điều gì về tài liệu đã cung cấp. Câu trả lời sẽ xuất hiện theo từng phần.",
        theme="soft",
        examples=[
            ["tại sao google lại tự làm chip?"],
            ["chip tensor có những ưu điểm gì so với đối thủ?"],
        ]
    )

    print("Khởi chạy giao diện Gradio...")
    iface.launch()
    ```

### Bước 3: Cập nhật Dependencies

-   **Công việc**: Đảm bảo file `requirements.txt` chứa cả `chromadb` và `gradio`. Sau đó chạy lệnh cài đặt:

    ```bash
    pip install -r requirements.txt
    ```

### Bước 4: Chạy ứng dụng

-   **Công việc**: Khởi động server Gradio bằng lệnh:

    ```bash
    python app.py
    ```
-   **Kết quả**: Mở URL được cung cấp trong terminal để thấy ứng dụng web với giao diện chat hỗ trợ streaming và các phiên trò chuyện riêng biệt.