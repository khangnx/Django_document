# Tóm tắt kiến thức về Embeddings và Vector DB

## HuggingFaceEmbeddings
- Là **class** trong LangChain để dùng mô hình embedding từ HuggingFace.
- Biến văn bản (chunk) thành **vector số học** để lưu vào **vector database** (FAISS, Chroma, Milvus…).
- Không giới hạn ở 2 mô hình (`all-MiniLM-L6-v2`, `paraphrase-multilingual-MiniLM-L12-v2`), có thể dùng bất kỳ mô hình tương thích trên HuggingFace Hub (ví dụ: `distiluse-base-multilingual-cased-v2`, `multi-qa-MiniLM-L6-cos-v1`…).

## So sánh mô hình phổ biến
- **all-MiniLM-L6-v2** → nhẹ, nhanh, chủ yếu tiếng Anh.
- **paraphrase-multilingual-MiniLM-L12-v2** → nặng hơn, chậm hơn, nhưng hỗ trợ đa ngôn ngữ (có tiếng Việt).
- **distiluse-base-multilingual-cased-v2** → đa ngôn ngữ, chất lượng tốt nhưng encode chậm hơn MiniLM.

👉 MiniLM thường được khuyến nghị vì cân bằng tốt giữa **tốc độ** và **chất lượng**.

## Các loại embeddings khác ngoài HuggingFace
- **OpenAIEmbeddings** → chất lượng cao, cần API key, có phí (tính theo token).
- **CohereEmbeddings** → dịch vụ Cohere, đa ngôn ngữ, tối ưu semantic search.
- **AzureOpenAIEmbeddings** → chạy qua Azure, tương tự OpenAI.
- **Google VertexAIEmbeddings** → tích hợp Google Cloud.
- **OllamaEmbeddings** → chạy local qua Ollama, không cần API key.
- **Local embeddings** → tự load mô hình từ `transformers` hoặc `sentence-transformers`.

## Ưu/nhược điểm
- **OpenAIEmbeddings**: chất lượng cao, dễ scale, nhưng có phí và cần internet.
- **HuggingFaceEmbeddings**: miễn phí, chạy local, nhưng tốc độ/độ chính xác phụ thuộc mô hình bạn chọn.
- **Cohere/Google/Azure**: dịch vụ cloud, có phí, nhưng mạnh mẽ và dễ tích hợp.
- **Ollama/local**: miễn phí, riêng tư, nhưng cần tài nguyên máy mạnh.

## Kết hợp với FastAPI
- FastAPI nhận input từ client → tạo embeddings → lưu vào vector_db → trả kết quả semantic search.
- Có thể dùng bất kỳ loại embeddings nào tùy nhu cầu (local vs cloud, miễn phí vs trả phí).

---
👉 Tóm lại:  
- Nếu muốn **miễn phí, chạy local** → HuggingFaceEmbeddings hoặc Ollama.  
- Nếu muốn **chất lượng cao, dễ scale** → OpenAIEmbeddings (có phí).  
- Nếu cần **đa ngôn ngữ** → chọn `paraphrase-multilingual-MiniLM-L12-v2` hoặc `distiluse-base-multilingual-cased-v2`.  
- Nếu chỉ cần **tiếng Anh, tốc độ nhanh** → chọn `all-MiniLM-L6-v2`.
