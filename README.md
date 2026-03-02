# Bidding System Backend

Dự án Bidding System Backend là một hệ thống quản lý đấu thầu toàn diện, được thiết kế với kiến trúc module hóa (Modular Architecture). Hệ thống không chỉ xử lý các quy trình nghiệp vụ đấu thầu cốt lõi mà còn tích hợp mạnh mẽ các luồng xử lý AI (RAG pipeline), quản lý phân quyền phức tạp và lưu trữ dữ liệu đa nền tảng.

## 🚀 Công nghệ sử dụng

- **Ngôn ngữ lập trình:** Python
- **Cơ sở dữ liệu chính:** SQL Server (Quản lý dữ liệu có cấu trúc cho người dùng, dự án thầu, phân quyền).
- **Cơ sở dữ liệu Vector (Vector DB):** ChromaDB.
  - *Mục đích:* Dùng để lưu trữ dữ liệu, tài liệu, và hồ sơ năng lực của công ty dưới dạng vector (embeddings) để so sánh và truy xuất.
  - *Lý do lựa chọn:* Vector DB cho phép tìm kiếm theo ngữ nghĩa (Semantic Search) vượt trội hơn tìm kiếm từ khóa truyền thống. Nó giúp hệ thống nhanh chóng tìm ra các tài liệu nội bộ có độ tương đồng cao với yêu cầu của hồ sơ mời thầu, từ đó cung cấp ngữ cảnh (context) chính xác cho các AI Agent thông qua kỹ thuật RAG (Retrieval-Augmented Generation) để tự động phân tích và lập hồ sơ dự thầu.
- **Lưu trữ Object (Object Storage):** MinIO (Lưu trữ file, tài liệu nội bộ độc lập).
- **Tích hợp AI Models & LLMs:** OpenAI, Gemini, Llama, DeepSeek, Docling.
- **Trình duyệt & Cào dữ liệu (Crawler):** MS Edge Driver.

## 📁 Cấu trúc thư mục dự án

Dự án được tổ chức theo mô hình Domain-Driven Design (DDD) kết hợp với Modular, phân chia rõ ràng trách nhiệm của từng thành phần:

Bidding_System_BE/
├── app/
│   ├── core/                 # Cấu hình cốt lõi: Logging, Security, Base Models và logic Phân quyền (RBAC/ABAC)
│   ├── infrastructure/       # Kết nối hạ tầng: Database (SQL Server), MinIO, VectorDB (ChromaDB)
│   ├── integrations/         # Tích hợp dịch vụ bên ngoài của bên thứ 3
│   │   ├── ai/               # AI Agents, Pipeline Ingest/Extract, RAG, Provider Models
│   │   ├── crawlers/         # Bot cào dữ liệu tự động (Edge Driver)
│   │   ├── google/           # Tích hợp Google Login, Google Drive MCP
│   │   └── microsoft/        # Tích hợp OneDrive
│   └── modules/              # Các module nghiệp vụ chính của hệ thống
│       ├── abac_config/      # Cấu hình quyền truy cập dựa trên thuộc tính (ABAC)
│       ├── ai_bidding/       # Xử lý nghiệp vụ thầu bằng AI
│       ├── auth/             # Xác thực người dùng (Login bằng Email)
│       ├── bidding/          # Phân hệ Đấu thầu (Project, Package, Requirement, Task, Result)
│       ├── crawler_config/   # Cấu hình các luồng cào dữ liệu
│       ├── drafting/         # Soạn thảo văn bản/hồ sơ thầu
│       ├── organization/     # Quản lý sơ đồ tổ chức công ty
│       ├── system/           # Cấu hình hệ thống chung
│       └── users/            # Quản lý người dùng
├── scripts/                  # Chứa các script chạy độc lập: khởi tạo DB, test AI (Llama, Deepseek), import dữ liệu
├── main.py                   # Điểm neo khởi chạy ứng dụng (Entry point)
└── requirements.txt          # Danh sách thư viện phụ thuộc

##✨ Các tính năng chính (Key Features)
1. Quản lý Đấu thầu (Bidding Management)
- Quản lý toàn bộ vòng đời của một dự án đấu thầu: Project -> Package -> Requirement -> Task -> Result.
- Theo dõi tiến độ công việc, phân công nhiệm vụ làm hồ sơ thầu.

2. Tích hợp AI & Xử lý tài liệu (AI & Document Processing)
- AI Agents: Có các agent chuyên biệt như bid_analysis (Phân tích hồ sơ), bid_preparation (Chuẩn bị thầu), planning_agent (Lập kế hoạch), construction (Biện pháp thi công).
- Data Ingestion Pipeline: Tự động trích xuất thông tin (Extract) từ file PDF (sử dụng Docling) và đưa vào VectorDB (Ingest/Ingest Advanced).
- Retrieval System: Hệ thống truy xuất thông tin bằng văn bản (Text Retrieval) và hình ảnh (Visual Retrieval).

3. Hệ thống Phân quyền Linh hoạt (Authorization)
- Hỗ trợ cả RBAC (Role-Based Access Control) và ABAC (Attribute-Based Access Control).
- Quản lý quyền truy cập chi tiết đến từng resource và hành động dựa trên thuộc tính của người dùng (ví dụ: chức vụ, phòng ban, phòng làm việc cụ thể).

4. Xác thực Người dùng (Authentication)
- Hệ thống đăng nhập được thiết kế chặt chẽ, sử dụng Email để định danh người dùng thay vì Username.
- Hỗ trợ đăng nhập qua hệ thống thứ 3 (Google Login).

5. Quản lý Lưu trữ Đồng bộ
- Đồng bộ và quản lý file linh hoạt thông qua MinIO (Self-hosted) hoặc các dịch vụ đám mây (Microsoft OneDrive, Google Drive).

6. Cào dữ liệu tự động (Web Crawler)
- Tích hợp Crawler Bot sử dụng trình duyệt Edge (msedgedriver) để tự động thu thập kết quả, thông tin gói thầu từ các trang mạng đấu thầu.

##🛠 Hướng dẫn Cài đặt & Khởi chạy
**Yêu cầu hệ thống:**
- Python 3.10+
- SQL Server đang hoạt động.
- Các dịch vụ MinIO, ChromaDB (có thể chạy qua Docker hoặc CSDL nội bộ).

**Các bước thực hiện:**
**Cài đặt thư viện:**
- pip install -r requirements.txt
**Cấu hình môi trường (Environment Variables):**
- Tạo file .env ở thư mục gốc và khai báo các thông tin kết nối (Chuỗi kết nối SQL Server, API Keys cho OpenAI/Gemini/Llama, thông tin MinIO, v.v.).

**Khởi tạo Database:**
- Chạy script để khởi tạo các bảng và dữ liệu mẫu trong SQL Server
python scripts/feature/init_db.py
python scripts/feature/check_db.py

**Khởi chạy Server:**
uvicorn main:app --reload hoặc python main.py

## 🧠 Giải thích Kiến trúc & Luồng xử lý cho Developer (Cần Đọc)

Dự án này có nhiều logic nghiệp vụ phức tạp liên quan đến AI, phân quyền và cào dữ liệu. Dưới đây là giải thích chi tiết về các cơ chế hoạt động ngầm để bạn dễ dàng nắm bắt code:

### 1. Cơ chế Phân quyền (RBAC kết hợp ABAC)
Hệ thống không chỉ dùng Role-Based Access Control (RBAC - quyền theo vai trò) mà còn dùng Attribute-Based Access Control (ABAC - quyền theo thuộc tính).
- **Vị trí code:** `app/core/permission/abac.py` và `permission_service.py`
- **Cách hoạt động:** Khi một API được gọi, hệ thống không chỉ kiểm tra xem user đó có quyền `CREATE_PROJECT` hay không (RBAC), mà còn kiểm tra xem user đó có thuộc đúng `org_id` (công ty), hoặc có đúng chức vụ/phòng ban để thao tác trên dự án cụ thể đó không (ABAC).
- **Lưu ý:** Bảng phân quyền được cấu hình động thông qua module `abac_config`.

### 2. Luồng xử lý AI & RAG Pipeline (Cốt lõi của hệ thống)
Hệ thống AI không chỉ đơn thuần là gọi API ChatGPT, mà là một hệ thống RAG (Retrieval-Augmented Generation) hoàn chỉnh để phân tích hồ sơ thầu.

* **Bước 1: Parse Tài liệu (Extract):** - Code sử dụng thư viện **Docling** (`app/integrations/ai/provider/docling_service.py`) để đọc các file PDF phức tạp (như hồ sơ mời thầu, biện pháp thi công) và chuyển đổi thành dạng Markdown/Text giữ nguyên cấu trúc.
* **Bước 2: Đưa vào VectorDB (Ingest):** - File text được cắt nhỏ (chunking) và nhúng (embedding) rồi lưu vào **ChromaDB** qua file `ingest.py` hoặc `ingest_advanced.py`.
* **Bước 3: AI Agents xử lý nghiệp vụ:**
  - Trong `app/integrations/ai/agent/`, mỗi file đảm nhận một "nhân viên AI" riêng biệt:
    - `bid_analysis.py`: Agent chuyên đọc và tóm tắt yêu cầu hồ sơ mời thầu.
    - `planning_agent.py`: Agent lập kế hoạch đấu thầu.
    - `construction.py`: Agent chuyên bóc tách và đề xuất Biện pháp thi công.
    - `drafting.py`: Agent hỗ trợ soạn thảo văn bản tự động.

### 3. Tổ chức Cơ sở dữ liệu (`all_models.py`)
- Mặc dù dự án chia thành các module nhỏ (`users`, `bidding`, `organization`...), nhưng do sử dụng cơ sở dữ liệu quan hệ (SQL Server) với các khóa ngoại (Foreign Keys) chéo nhau, nên dự án sử dụng `app/infrastructure/database/all_models.py`.
- **Mục đích:** File này dùng để import toàn bộ các model từ các module khác nhau vào một nơi trước khi khởi tạo DB. Điều này giúp SQLAlchemy/ORM nhận diện được toàn bộ liên kết (Relationships) giữa các bảng mà không bị lỗi vòng lặp import (Circular Import).

### 4. Tích hợp Crawler (Thu thập dữ liệu thầu)
- **Vị trí code:** `app/integrations/crawlers/`
- **Cách hoạt động:** Việc thu thập dữ liệu thầu không dùng Request HTTP thông thường (vì dễ bị chặn hoặc dính Captcha), mà hệ thống sử dụng **Trình duyệt giả lập (Selenium/Edge Driver)** thông qua file `msedgedriver.exe`.
- `crawler_bot.py` sẽ mở trình duyệt ngầm, điều hướng đến trang mạng đấu thầu, và `result_crawler.py` sẽ bóc tách các kết quả thầu mang về lưu vào DB.

### 5. Quản lý File đa luồng (Storage & Integrations)
Hệ thống hỗ trợ lưu trữ file từ nhiều nguồn khác nhau:
- **MinIO (Self-hosted):** Xử lý luồng upload file nội bộ chính của hệ thống (`app/infrastructure/storage/minio_client.py`).
- **Google Drive & OneDrive:** Hỗ trợ kết nối với kho lưu trữ bên ngoài của doanh nghiệp qua MCP (Model Context Protocol) và Graph API (`app/integrations/google` và `app/integrations/microsoft`).
- File rác/tạm thời trong quá trình AI phân tích PDF (như file `Bien phap thi cong T3.pdf`) sẽ được đưa vào `app/infrastructure/temp_storage/temp_uploads/` trước khi dọn dẹp.

### 6. Cách đăng nhập
- User đăng nhập hoàn toàn bằng **Email** thay vì Username truyền thống (logic trong `app/modules/auth/`).
- Có tích hợp đăng nhập thẳng bằng Microsoft (`auth/router.py`).
