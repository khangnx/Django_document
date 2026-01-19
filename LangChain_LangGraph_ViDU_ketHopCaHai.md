### Đề bài: Tôi muốn dung kết hợp python Flask và LangChain nạp dữ lieu từ nhiều file excel về phân tích thị thị trường chứng khoán, 3 multi-agent cho đọc dữ lieu, tổng hợp report và đưa ra lời khuyện về mua cổ phiếu

### Kiến trúc tổng quan
- Flask API: nhận yêu cầu từ người dùng (upload file Excel, chọn mã cổ phiếu, khung thời gian), trả về báo cáo và khuyến nghị.
- Data layer (Excel → DataFrame): đọc nhiều file Excel bằng pandas, hợp nhất, chuẩn hóa cột (mã cổ phiếu, ngày, giá, khối lượng…).
- LangGraph (multi-agent):
- Agent Reader: đọc/tiền xử lý dữ liệu từ Excel, kiểm tra chất lượng dữ liệu.
- Agent Summarizer: tổng hợp báo cáo (xu hướng, biến động, thanh khoản).
- Agent Advisor: đưa ra khuyến nghị mang tính nguyên tắc (không phải tư vấn cá nhân), dựa trên tín hiệu cơ bản (ví dụ: MA, RSI, khối lượng).
- Model LLM: GPT hoặc mô hình tương thích (qua LangChain).

### Cấu trúc thư mục

```
   stock-app/
├─ app.py                # Flask API
├─ agents.py             # Định nghĩa các agent LangGraph
├─ data_utils.py         # Đọc/chuẩn hóa Excel
├─ requirements.txt
└─ uploads/              # Lưu file Excel tạm
```

## File: requirements.txt

```
flask
pandas
langchain
langchain-openai
langgraph
python-dotenv
```

## File: data_utils.py — đọc và hợp nhất Excel

```
import pandas as pd
from pathlib import Path

REQUIRED_COLUMNS = ["symbol", "date", "open", "high", "low", "close", "volume"]

def load_excel_files(file_paths):
    dfs = []
    for fp in file_paths:
        df = pd.read_excel(fp)
        df = normalize_columns(df)
        dfs.append(df)
    if not dfs:
        return pd.DataFrame(columns=REQUIRED_COLUMNS)
    merged = pd.concat(dfs, ignore_index=True)
    merged["date"] = pd.to_datetime(merged["date"])
    merged = merged.sort_values(["symbol", "date"])
    return merged

def normalize_columns(df):
    # Chuẩn hóa tên cột về định dạng chuẩn
    col_map = {c.lower().strip(): c for c in df.columns}
    df = df.rename(columns={v: k for k, v in col_map.items()})
    # Map các biến thể về chuẩn
    alias = {
        "ticker": "symbol",
        "code": "symbol",
        "ngay": "date",
        "gia_dong_cua": "close",
        "gia_mo_cua": "open",
        "khoi_luong": "volume"
    }
    for a, std in alias.items():
        if a in df.columns and std not in df.columns:
            df = df.rename(columns={a: std})
    # Điền thiếu cột
    for col in REQUIRED_COLUMNS:
        if col not in df.columns:
            df[col] = None
    return df[REQUIRED_COLUMNS]
```

## File: agents.py — LangChain Agent và LangGraph flow multi-agent

```
from langgraph.graph import StateGraph, END
from langchain_openai import ChatOpenAI

# Trạng thái chia sẻ giữa các agent
class State(dict):
    pass

# Khởi tạo LLM (cần OPENAI_API_KEY trong môi trường)
llm = ChatOpenAI(model="gpt-4o-mini")

# Agent 1: Reader — kiểm tra dữ liệu, tính vài chỉ báo cơ bản
def reader_agent(state: State):
    import pandas as pd
    df = state["df"]
    symbol = state.get("symbol")

    if symbol:
        df = df[df["symbol"] == symbol]

    # Loại bỏ dòng thiếu close
    df = df.dropna(subset=["close"])
    # Tính MA 20, MA 50
    df = df.sort_values("date")
    df["ma20"] = df["close"].rolling(20).mean()
    df["ma50"] = df["close"].rolling(50).mean()
    # Tính thay đổi % ngày
    df["pct_change"] = df["close"].pct_change() * 100

    # Tóm tắt nhanh
    summary = {
        "rows": len(df),
        "date_range": f"{df['date'].min().date()} → {df['date'].max().date()}" if len(df) else "N/A",
        "latest_close": float(df["close"].iloc[-1]) if len(df) else None,
        "latest_ma20": float(df["ma20"].iloc[-1]) if len(df) else None,
        "latest_ma50": float(df["ma50"].iloc[-1]) if len(df) else None,
        "avg_volume": float(df["volume"].dropna().mean()) if len(df) else None,
    }

    state["df_processed"] = df
    state["data_summary"] = summary
    return state

# Agent 2: Summarizer — tạo báo cáo xu hướng
def summarizer_agent(state: State):
    df = state["df_processed"]
    summary = state["data_summary"]
    symbol = state.get("symbol", "N/A")

    # Chuẩn bị prompt cho LLM
    prompt = (
        f"Bạn là chuyên gia phân tích dữ liệu chứng khoán.\n"
        f"Mã: {symbol}\n"
        f"Tổng quan dữ liệu: {summary}\n"
        f"Hãy viết báo cáo ngắn gọn (xu hướng, biến động, thanh khoản) dựa trên số liệu.\n"
        f"Tránh khuyến nghị mua/bán ở phần này."
    )
    report = llm.invoke(prompt).content
    state["report"] = report
    return state

# Agent 3: Advisor — đưa ra khuyến nghị mang tính nguyên tắc
def advisor_agent(state: State):
    df = state["df_processed"]
    summary = state["data_summary"]
    symbol = state.get("symbol", "N/A")

    # Tín hiệu đơn giản: MA20 vs MA50, đà tăng (% thay đổi gần đây), khối lượng
    signal = "neutral"
    if len(df) >= 50:
        latest_ma20 = df["ma20"].iloc[-1]
        latest_ma50 = df["ma50"].iloc[-1]
        recent_change = df["pct_change"].iloc[-5:].mean()  # trung bình 5 ngày
        avg_vol = df["volume"].dropna().tail(20).mean()

        if latest_ma20 and latest_ma50 and latest_ma20 > latest_ma50 and recent_change and recent_change > 0:
            signal = "bullish"
        elif latest_ma20 and latest_ma50 and latest_ma20 < latest_ma50 and recent_change and recent_change < 0:
            signal = "bearish"

    prompt = (
        f"Bạn là cố vấn đầu tư, nhưng KHÔNG đưa ra tư vấn cá nhân.\n"
        f"Mã: {symbol}\n"
        f"Tín hiệu tổng hợp: {signal}\n"
        f"Tóm tắt dữ liệu: {summary}\n"
        f"Hãy đưa ra khuyến nghị mang tính nguyên tắc (ví dụ: chờ xác nhận, quản trị rủi ro, đặt stop-loss, đa dạng hóa), "
        f"nhấn mạnh rằng đây không phải lời khuyên tài chính cá nhân."
    )
    advice = llm.invoke(prompt).content
    state["advice"] = advice
    return state

# Xây dựng đồ thị multi-agent
def build_workflow():
    workflow = StateGraph(State)
    workflow.add_node("reader", reader_agent)
    workflow.add_node("summarizer", summarizer_agent)
    workflow.add_node("advisor", advisor_agent)

    workflow.set_entry_point("reader")
    workflow.add_edge("reader", "summarizer")
    workflow.add_edge("summarizer", "advisor")
    workflow.add_edge("advisor", END)
    return workflow.compile()
```

## File: app.py — Flask API

```
import os
from flask import Flask, request, jsonify
from werkzeug.utils import secure_filename
from pathlib import Path
import pandas as pd

from data_utils import load_excel_files
from agents import build_workflow

UPLOAD_DIR = Path("uploads")
UPLOAD_DIR.mkdir(exist_ok=True)

app = Flask(__name__)
app.config["UPLOAD_FOLDER"] = str(UPLOAD_DIR)

# Khởi tạo workflow LangGraph
workflow_app = build_workflow()

@app.route("/analyze", methods=["POST"])
def analyze():
    """
    Form-data:
      - files[]: nhiều file Excel
      - symbol: mã cổ phiếu (tùy chọn)
    """
    files = request.files.getlist("files[]")
    symbol = request.form.get("symbol", None)

    if not files:
        return jsonify({"error": "Vui lòng upload ít nhất một file Excel"}), 400

    file_paths = []
    for f in files:
        filename = secure_filename(f.filename)
        fp = UPLOAD_DIR / filename
        f.save(fp)
        file_paths.append(fp)

    # Đọc & hợp nhất dữ liệu
    df = load_excel_files(file_paths)

    # Gọi multi-agent
    final_state = workflow_app.invoke({"df": df, "symbol": symbol})

    result = {
        "symbol": symbol,
        "data_summary": final_state.get("data_summary"),
        "report": final_state.get("report"),
        "advice": final_state.get("advice"),
    }
    return jsonify(result), 200

if __name__ == "__main__":
    # Cần đặt biến môi trường OPENAI_API_KEY trước khi chạy
    # Ví dụ: setx OPENAI_API_KEY "your_key" (Windows) hoặc export OPENAI_API_KEY=...
    app.run(host="0.0.0.0", port=5000, debug=True)
```

## Cách chạy thử
- Cài đặt thư viện:

```
pip install -r requirements.txt
```

- Đặt API key:
- Windows:

  ```
  setx OPENAI_API_KEY "your_key"
  ```
  
- macOS/Linux:

  ```
  export OPENAI_API_KEY=your_key
  ```
  
- Chạy Flask:

```
python app.py
```

- Gửi yêu cầu (ví dụ bằng curl hoặc Postman):

```
curl -X POST http://localhost:5000/analyze \
  -F "files[]=@data1.xlsx" \
  -F "files[]=@data2.xlsx" \
  -F "symbol=VNM"

```


