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

## Hình minh họa nguyên quá trình
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b7270ee2-9510-446e-a05f-46f871ef17d5" />

## Trong hình, bạn sẽ thấy tiến trình được chia thành 5 bước rõ ràng:

- Nhập File/Tài Liệu → dùng Document Loaders (PDF, TXT, CSV, Web…).

- Tách Nhỏ Văn Bản → dùng Text Splitters (Recursive, Token, Markdown…).

- Tạo Embeddings → dùng HuggingFaceEmbeddings hoặc các loại embeddings khác.

- Lưu Vector Database → FAISS, Chroma… để lưu trữ vector.

- Truy Vấn/Trả Lời → Semantic Search để tìm kiếm ngữ nghĩa và trả kết quả.

--> Như vậy bạn có thể hình dung toàn bộ pipeline từ file gốc → chunk → embeddings → vector DB → query một cách trực quan.

# Nói về chunk data

<img width="933" height="141" alt="image" src="https://github.com/user-attachments/assets/d61b063b-1ea1-43bc-9b23-4b0f89ece879" />
<img width="917" height="695" alt="image" src="https://github.com/user-attachments/assets/4bb5ddf9-ee0b-41ee-940d-3fbdb717bd65" />
<img width="871" height="505" alt="image" src="https://github.com/user-attachments/assets/20038476-ae5f-451c-8e9a-47ddbecda057" />

# Nói về ChromaDB

<img width="945" height="541" alt="image" src="https://github.com/user-attachments/assets/36a7af10-6620-4f98-b8ef-ca299d5fb63a" />

## Note: nói về " persist_directory=VECTOR_DB_DIR" trong khai báo

```
vector_store = Chroma.from_documents(
            documents=all_chunks,
            embedding=embeddings,
            persist_directory=VECTOR_DB_DIR
        )
```
<img width="965" height="278" alt="image" src="https://github.com/user-attachments/assets/31cafa1a-3251-4808-bfdb-06cdb4c9629a" />

## Nói đến cái cuối trong AI, bộ não LLM (Large Language Model)của AI agent

<img width="1002" height="532" alt="image" src="https://github.com/user-attachments/assets/1cf7ebc3-f30f-4b66-8693-618cb1d438e9" />
<img width="933" height="718" alt="image" src="https://github.com/user-attachments/assets/3aad2e9d-455b-4e17-8325-fbc222fad015" />
<img width="757" height="182" alt="image" src="https://github.com/user-attachments/assets/17620449-c9f5-49cd-a880-6855286c4379" />

## Flow toàn hệ thốnf 1 con AI agent
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/eaafd1ec-6a86-4fef-9347-5f390dc28320" />
<img width="717" height="642" alt="image" src="https://github.com/user-attachments/assets/0dc915ae-5676-4a32-b2f9-508f6ffb93a8" />
<img width="936" height="122" alt="image" src="https://github.com/user-attachments/assets/c0e10047-d68a-4911-ac21-e924b4b99643" />











