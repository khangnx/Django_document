# CẨM NANG CÁC DÒNG MÔ HÌNH VÀ THUẬT TOÁN TRONG AI/ML

Tài liệu này phân loại và phân tích sâu các kiến trúc mô hình nằm bên trong 5 framework phổ biến nhất hiện nay, chia theo bản chất toán học từ Deep Learning (Học sâu), Generative AI (AI tạo sinh) cho đến Ensemble Learning (Học máy tăng cường dạng cây).

---

## PHẦN 1: CÁC MÔ HÌNH TRONG PYTORCH & TENSORFLOW
Do PyTorch và TensorFlow đều là các thư viện nền tảng (Low-level frameworks) để xây dựng mạng thần kinh nhân tạo (Neural Networks), nên chúng cùng chia sẻ các kiến trúc mô hình kinh điển dưới đây. Sự khác biệt chỉ nằm ở cách viết code triển khai.

### 1.1. Mạng thần kinh nhân tạo truyền thẳng (Multilayer Perceptron - MLP / ANN)
* **Đặc điểm:** Kiến trúc cơ bản nhất gồm các tầng dữ liệu (Layers) kết nối đầy đủ với nhau (Fully Connected). Dữ liệu chỉ đi theo một chiều từ đầu vào qua các tầng ẩn (Hidden Layers) đến đầu ra.
* **Hữu dụng nhất khi:** Xử lý dữ liệu dạng bảng (Tabular Data) đơn giản, phân loại nhị phân (Binary Classification) hoặc dự báo giá trị số (Regression) khi số lượng thuộc tính ít và không có yếu tố không gian/thời gian.

### 1.2. Mạng thần kinh tích chập (Convolutional Neural Networks - CNN)
* **Đặc điểm:** Sử dụng các bộ lọc bộ lọc tích chập (Kernel/Filters) quét qua dữ liệu để tự động trích xuất các đặc trưng không gian (Spatial Features) từ thấp đến cao (cạnh, góc, hình khối, vật thể).
* **Hữu dụng nhất khi:** * **Thị giác máy tính (Computer Vision):** Phân loại ảnh, phát hiện vật thể (Object Detection như mô hình YOLO viết trên PyTorch), phân vùng hình ảnh (Segmentation).
    * **Ứng dụng thực tế của bạn:** Quét ảnh chụp góc nhà, phân loại chất lượng nội thất, đếm số tầng từ hình ảnh/video.

### 1.3. Mạng thần kinh tái phát (Recurrent Neural Networks - RNN & LSTM/GRU)
* **Đặc điểm:** Có kiến trúc vòng lặp cho phép lưu trữ thông tin của các bước tính toán trước đó (Bộ nhớ ngắn hạn - Hidden State). Dòng cải tiến LSTM và GRU bổ sung các "Cổng" (Gates) để giải quyết lỗi tiêu biến đạo hàm (Vanishing Gradient) khi chuỗi quá dài.
* **Hữu dụng nhất khi:** Xử lý dữ liệu dạng chuỗi có tính chất thời gian (Sequential Data).
    * **Ứng dụng:** Dự báo chuỗi thời gian (Giá vàng, chứng khoán), phân tích quan điểm văn bản (Sentiment Analysis).

### 1.4. Kiến trúc Transformer (Vision Transformer - ViT & Bert/GPT)
* **Đặc điểm:** Loại bỏ hoàn toàn vòng lặp của RNN, sử dụng cơ chế **Tự chú ý (Self-Attention)** để tính toán mối quan hệ giữa TẤT CẢ các phần tử trong chuỗi cùng một lúc. Kiến trúc này có khả năng song song hóa cực cao trên GPU.
* **Hữu dụng nhất khi:** * **Xử lý ngôn ngữ (NLP):** Làm lõi cho các mô hình dịch thuật, hiểu văn bản.
    * **Thị giác máy tính nâng cao (ViT):** Thay thế CNN trong việc hiểu bối cảnh bức ảnh lớn một cách toàn diện hơn.

---

## PHẦN 2: CÁC DÒNG MÔ HÌNH CỦA CLAUDE (ANTHROPIC)
Khác với hai framework trên, Claude là một **Đại mô hình ngôn ngữ (LLM)** thương mại đã được huấn luyện hoàn chỉnh dựa trên kiến trúc Transformer độc quyền của Anthropic. Claude được chia làm các "Dòng" (Models) tối ưu cho từng phân khúc tài nguyên:

### 2.1. Dòng Claude Haiku (Ví dụ: Claude 3 Haiku, Claude 3.5 Haiku)
* **Đặc điểm:** Mô hình có kích thước tham số nhỏ nhất (Lightweight), tốc độ phản hồi siêu tốc (gần như tức thì) và chi phí API rẻ nhất.
* **Hữu dụng nhất khi:** Các tác vụ nhẹ, lặp đi lặp lại nhiều lần, yêu cầu thời gian thực (Real-time).
    * *Sử dụng:* Phân loại ý định câu hỏi của khách (Intent Parsing), chuẩn hóa định dạng dữ liệu (Text to JSON), tóm tắt văn bản ngắn.

### 2.2. Dòng Claude Sonnet (Ví dụ: Claude 3.5 Sonnet)
* **Đặc điểm:** Dòng mô hình cân bằng hoàn hảo (Sweet Spot) giữa tốc độ và trí thông minh. Hiện tại, bản 3.5 Sonnet được đánh giá là mô hình có tư duy logic, khả năng viết code lập trình và phân tích cấu trúc file phức tạp hàng đầu thế giới.
* **Hữu dụng nhất khi:** Làm bộ não điều phối cho AI Agent, giải các bài toán lập luận đa bước (Multi-step reasoning), lập kế hoạch hệ thống, bóc tách và phân tích các bảng biểu đồ thị phức tạp từ tài liệu PDF.

### 2.3. Dòng Claude Opus (Ví dụ: Claude 3 Opus)
* **Đặc điểm:** Mô hình cao cấp nhất, kích thước khổng lồ, tư duy sâu sắc, hiểu ngữ cảnh văn hóa phức tạp nhưng tốc độ chạy chậm và chi phí API rất cao.
* **Hữu dụng nhất khi:** Các bài toán nghiên cứu chiến lược, dịch thuật văn chương, phân tích các tài liệu pháp lý cực kỳ đồ sộ hoặc các phép toán logic mang tính học thuật cao.

---

## PHẦN 3: CÁC MÔ HÌNH TRONG XGBOOST VÀ LIGHTGBM
XGBoost và LightGBM không có hàng trăm kiến trúc như mạng thần kinh. Bản chất của chúng là Framework xây dựng trên thuật toán **Cây quyết định tăng cường gradient (Gradient Boosting Decision Trees - GBDT)**. Tuy nhiên, chúng cung cấp các "Mô hình lõi" (Cơ chế tối ưu hàm mất mát) khác nhau thông qua tham số `objective` hoặc các lớp Wrapper (Lớp bọc):

### 3.1. Mô hình Dự báo giá trị liên tục (Regression Model - `XGBRegressor` / `LGBMRegressor`)
* **Đặc điểm:** Sử dụng hàm mất mát là Sai số bình phương trung bình (MSE) hoặc Sai số tuyệt đối trung bình (MAE). Hệ thống cây quyết định sẽ chẻ nhánh để liên tục giảm sai số của giá trị dự đoán so với thực tế.
* **Hữu dụng nhất khi:** Cần dự báo một con số cụ thể.
    * **Ứng dụng thực tế của bạn:** Dự đoán giá bán nhà đất (raw_price_billion) dựa trên diện tích, số tầng, chất lượng nội thất và tọa độ địa lý.

### 3.2. Mô hình Phân loại nhị phân (Binary Classification - `XGBClassifier` / `LGBMClassifier` với `binary:logistic`)
* **Đặc điểm:** Đầu ra của mô hình là một giá trị xác suất nằm trong khoảng từ 0 đến 1 (Xác suất xảy ra một sự kiện).
* **Hữu dụng nhất khi:** Cần phân loại bài toán thành 2 trạng thái Đúng/Sai.
    * *Sử dụng:* Dự đoán một căn nhà có bị dính quy hoạch hay không (`True`/`False`), xác định một giao dịch bất động sản có dấu hiệu lừa đảo/thao túng giá hay không.

### 3.3. Mô hình Phân loại đa lớp (Multi-class Classification - với `multi:softprob`)
* **Đặc điểm:** Đầu ra là một mảng xác suất cho nhiều nhãn phân loại khác nhau (Tổng xác suất các nhãn bằng 1).
* **Hữu dụng nhất khi:** Cần phân nhóm dữ liệu vào nhiều hơn 2 danh mục.
    * *Sử dụng:* Phân loại phân khúc bất động sản dựa trên thông số (Ví dụ: Nhà cấp 4, Nhà phố, Biệt thự, Căn hộ chung cư).

### 3.4. Mô hình Xếp hạng (Ranking Model - `XGBRanker` / `LGBMRanker`)
* **Đặc điểm:** Thay vì tối ưu hóa điểm số của từng dòng độc lập, mô hình tối ưu hóa thứ tự sắp xếp (Relevance Score) của một danh sách dữ liệu (sử dụng thuật toán LambdaMART).
* **Hữu dụng nhất khi:** Xây dựng hệ thống gợi ý (Recommendation System) hoặc tìm kiếm.
    * *Sử dụng:* Sắp xếp và gợi ý cho khách hàng danh sách 5 căn nhà "hời" nhất, đáng mua nhất xếp theo thứ tự ưu tiên phù hợp với tài chính của họ.
