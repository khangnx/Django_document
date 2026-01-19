```
# app.py
import os
from flask import Flask, request, jsonify
from dotenv import load_dotenv

# LangChain core
from langchain.chat_models import ChatOpenAI
from langchain.schema import SystemMessage, HumanMessage
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain

# Optional: enable CORS if you call from a browser frontend
try:
    from flask_cors import CORS
    CORS_ENABLED = True
except ImportError:
    CORS_ENABLED = False

# -----------------------------
# 1) Khởi tạo Flask & cấu hình
# -----------------------------
load_dotenv()  # đọc biến môi trường từ .env nếu có
app = Flask(__name__)
if CORS_ENABLED:
    CORS(app)

# Lấy API key từ biến môi trường
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")
if not OPENAI_API_KEY:
    raise RuntimeError("Vui lòng đặt biến môi trường OPENAI_API_KEY trước khi chạy.")

# -----------------------------
# 2) Khởi tạo mô hình GPT qua LangChain
# -----------------------------
# Bạn có thể đổi model_name thành "gpt-4o-mini" hoặc "gpt-4o" nếu có quyền truy cập
llm = ChatOpenAI(
    openai_api_key=OPENAI_API_KEY,
    model_name="gpt-3.5-turbo",
    temperature=0.3,  # giảm 'sáng tạo' để câu trả lời ổn định hơn
)

# -----------------------------
# 3) Bộ nhớ hội thoại theo session
# -----------------------------
# Lưu trữ memory theo session_id để giữ ngữ cảnh cho từng người dùng
session_memories = {}  # dict: {session_id: ConversationBufferMemory}

def get_or_create_memory(session_id: str) -> ConversationBufferMemory:
    if session_id not in session_memories:
        session_memories[session_id] = ConversationBufferMemory(return_messages=True)
    return session_memories[session_id]

# -----------------------------
# 4) Tạo ConversationChain
# -----------------------------
# ConversationChain sẽ dùng LLM + Memory để giữ ngữ cảnh hội thoại
def build_chain(memory: ConversationBufferMemory) -> ConversationChain:
    # System prompt giúp định hình vai trò của chatbot
    system_prompt = (
        "Bạn là một trợ lý AI nói tiếng Việt, trả lời ngắn gọn, rõ ràng, "
        "ưu tiên tính chính xác và hữu ích. Nếu câu hỏi không rõ, hãy hỏi lại."
    )
    # ConversationChain không nhận SystemMessage trực tiếp,
    # ta sẽ thêm vào memory như một thông điệp khởi tạo.
    if not memory.chat_memory.messages:
        memory.chat_memory.add_message(SystemMessage(content=system_prompt))

    chain = ConversationChain(
        llm=llm,
        memory=memory,
        verbose=False,  # bật True nếu muốn log chi tiết
    )
    return chain

# -----------------------------
# 5) Flask routes
# -----------------------------
@app.route("/health", methods=["GET"])
def health():
    return jsonify({"status": "ok"})

@app.route("/chat", methods=["POST"])
def chat():
    """
    Body JSON:
    {
      "message": "câu hỏi của người dùng",
      "session_id": "id phiên (tùy chọn, nếu không có sẽ tạo mới)"
    }
    """
    data = request.get_json(force=True) or {}
    user_message = data.get("message", "").strip()
    session_id = data.get("session_id", "default")

    if not user_message:
        return jsonify({"error": "Thiếu 'message'"}), 400

    # Lấy/khởi tạo memory theo session
    memory = get_or_create_memory(session_id)
    chain = build_chain(memory)

    # Thêm HumanMessage vào memory để giữ lịch sử
    memory.chat_memory.add_message(HumanMessage(content=user_message))

    # Gọi chain để sinh câu trả lời
    try:
        response_text = chain.run(user_message)
    except Exception as e:
        return jsonify({"error": str(e)}), 500

    return jsonify({
        "session_id": session_id,
        "message": user_message,
        "answer": response_text
    })

# -----------------------------
# 6) Chạy ứng dụng
# -----------------------------
if __name__ == "__main__":
    # Ví dụ: chạy trên localhost:5000
    app.run(host="0.0.0.0", port=5000, debug=True)
```
## Giải thích từng phần
### Cấu hình và khởi tạo
- Flask app: Tạo ứng dụng web đơn giản với các route /health và /chat.
- OpenAI API key: Lấy từ biến môi trường OPENAI_API_KEY. Bạn đặt trong hệ thống hoặc file .env.
- CORS (tùy chọn): Cho phép gọi API từ frontend (React/Vue) nếu cần.
### Mô hình GPT qua LangChain
- ChatOpenAI: Wrapper của LangChain để gọi GPT.
- model_name: Dùng gpt-3.5-turbo cho phổ biến; có thể đổi sang model khác nếu bạn có quyền.
- temperature: Điều chỉnh độ “sáng tạo”—giảm để câu trả lời ổn định, tăng để đa dạng.
### Bộ nhớ hội thoại theo session
- ConversationBufferMemory: Lưu lịch sử hội thoại để giữ ngữ cảnh.
- session_memories: Dict lưu memory theo session_id, giúp mỗi người dùng có ngữ cảnh riêng.
### ConversationChain
- System prompt: Định hình vai trò và phong cách trả lời của chatbot.
- Chain: Kết hợp LLM + Memory để tạo hội thoại liên tục.
- Thêm HumanMessage: Mỗi câu hỏi của người dùng được thêm vào memory trước khi gọi chain.
### Endpoint /chat
- Input: JSON gồm message và session_id (tùy chọn).
- Xử lý: Lấy/khởi tạo memory, xây chain, chạy chain.run(message).
- Output: Trả về answer cùng session_id để frontend tiếp tục hội thoại.
=========================================================================================================================================
## Cách chạy nhanh
- Bước 1: Cài đặt thư viện
- Label: pip
  
```
pip install flask python-dotenv langchain openai flask-cors
```
- Bước 2: Đặt API key
- Label: Windows PowerShell
  
```
setx OPENAI_API_KEY "YOUR_KEY_HERE"
```
- Label: macOS/Linux (bash)
  
```
export OPENAI_API_KEY="YOUR_KEY_HERE"
```

- Bước 3: Chạy server
- Label: run
  
```
python app.py
```

- Gọi thử bằng cURL hoặc Postman- cURL:
  
```
curl -X POST http://localhost:5000/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"Xin chào, bạn là ai?", "session_id":"user123"}'
```

- Kết quả mẫu:
  
```
{
  "session_id": "user123",
  "message": "Xin chào, bạn là ai?",
  "answer": "Mình là trợ lý AI nói tiếng Việt, sẵn sàng hỗ trợ bạn."
}
```

## Mở rộng nhanh- Thêm công cụ (tools): Kết nối API nội bộ, cơ sở dữ liệu, tìm kiếm tài liệu (RAG) qua LangChain Tools/Document Loaders.
- Bảo mật: Thêm rate limiting, xác thực token cho endpoint /chat.
- Lưu trữ memory: Thay dict bằng Redis hoặc database để giữ ngữ cảnh lâu dài.
- Streaming: Dùng server-sent events hoặc websockets để stream token trả lời theo thời gian thực.

