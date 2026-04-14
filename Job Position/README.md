# DANH MỤC CÁC VAI TRÒ TRONG NGÀNH IT

## 1. SOFTWARE DEVELOPMENT

### 1.1 Backend Developer
**Vai trò & trách nhiệm (thực tế):**
* Thiết kế và xây dựng **API (REST/GraphQL)** để frontend/mobile gọi.
* Xử lý **business logic**: validate dữ liệu, tính toán, rule hệ thống.
* **Thiết kế database**:
    * Quan hệ bảng (ERD)
    * Index để tối ưu query
* **Tối ưu hiệu năng**:
    * Giảm latency API
    * Caching (Redis)
* **Làm việc với hệ thống ngoài**: Payment, email, third-party APIs.
* **Debug lỗi production**: Log, trace, fix bug khi hệ thống đang chạy.
* Viết test (unit/integration) và tham gia code review.

👉 **Công việc hằng ngày:** Nhận task từ Jira → code → test → push → review → deploy.

**Kinh nghiệm & Kỹ thuật:**
* **Intern:** Biết basic API + project nhỏ.
* **Junior:** Có project thật (CRUD + auth + DB).
* **Tech phải biết:** Python / Node.js / Java, REST API, SQL, Git.
* **Nên biết:** FastAPI / Django, Redis, Docker, Clean Architecture.

### 1.2 Frontend Developer
**Vai trò & trách nhiệm:**
* Xây dựng giao diện từ design (Figma).
* Chuyển UI thành code (pixel-perfect).
* Gọi API backend và hiển thị dữ liệu.
* Quản lý state (global/local).
* **Tối ưu UX**: Loading, error handling, fix bug cross-browser.

👉 **Công việc hằng ngày:** Code UI → connect API → fix bug UI → optimize performance.

**Kinh nghiệm & Kỹ thuật:**
* **Intern:** Làm được UI + call API.
* **Junior:** Làm project hoàn chỉnh.
* **Tech phải biết:** HTML, CSS, JS, React/Vue.
* **Nên biết:** TypeScript, Next.js, Responsive design.

### 1.3 Fullstack Developer
**Vai trò & trách nhiệm:**
* Làm cả frontend + backend cho feature nhỏ hoặc product.
* Thiết kế flow end-to-end: UI → API → DB.
* Tự deploy project nhỏ.

👉 **Thực tế:** Các Startup rất thích role này.

**Kinh nghiệm & Kỹ thuật:**
* **Kinh nghiệm:** 1–2 năm.
* **Tech phải biết:** 1 stack full (VD: React + Node / React + FastAPI).
* **Nên biết:** DevOps basic, System design.

### 1.4 Mobile Developer
**Vai trò & trách nhiệm:**
* Xây dựng app mobile, làm việc với API.
* Handle offline data, cache.
* Fix bug trên nhiều device.

👉 **Thực tế:** Debug rất nhiều do device fragmentation.

**Tech phải biết:** Kotlin / Swift. **Nên biết:** Flutter / React Native.

---

## 2. DATA & AI

### 2.1 Data Engineer
**Vai trò & trách nhiệm:**
* **Xây dựng pipeline dữ liệu**: Lấy data từ nhiều nguồn (DB, API, logs) → Transform → Load vào data warehouse.
* Làm sạch dữ liệu (data cleaning).
* Đảm bảo data không lỗi và cập nhật đúng thời gian.
* **Tối ưu pipeline**: Chạy nhanh hơn, ít lỗi hơn.

👉 **Công việc hằng ngày:** Fix pipeline fail, viết job ETL, monitor data.

**Kinh nghiệm & Kỹ thuật:**
* **Kinh nghiệm:** 1–2 năm (khó hơn backend entry).
* **Tech phải biết:** Python, SQL.
* **Nên biết:** Airflow, Spark, Kafka.

### 2.2 Data Analyst
**Vai trò & trách nhiệm:**
* Query dữ liệu để trả lời câu hỏi business.
* Tạo dashboard (BI tools).
* Phân tích user behavior và revenue.

👉 **Thực tế:** Làm việc nhiều với team business hơn dev.

**Kinh nghiệm & Kỹ thuật:**
* **Kinh nghiệm:** 0–1 năm.
* **Tech phải biết:** SQL, Excel.
* **Nên biết:** Power BI / Tableau, Python.

### 2.3 Data Scientist
**Vai trò & trách nhiệm:**
* Xây model machine learning, training + tuning model.
* Đánh giá model (accuracy, precision...).
* Deploy model (có khi cần backend knowledge).

👉 **Thực tế:** 70% là data cleaning + feature engineering.

**Kinh nghiệm & Kỹ thuật:**
* **Kinh nghiệm:** 1–2 năm.
* **Tech phải biết:** Python, ML cơ bản.
* **Nên biết:** Deep Learning, Statistics.

---

## 3. DEVOPS & CLOUD

### 3.1 DevOps Engineer
**Vai trò & trách nhiệm:**
* Xây CI/CD pipeline (Auto build → test → deploy).
* Quản lý server, monitor hệ thống (CPU, RAM, logs).
* Fix lỗi deploy.

👉 **Thực tế:** Khi hệ thống down, DevOps là người bị gọi đầu tiên.

**Tech phải biết:** Linux, Docker. **Nên biết:** Kubernetes, AWS.

### 3.2 Cloud Engineer
**Vai trò & trách nhiệm:**
* Thiết kế hệ thống trên cloud, tối ưu chi phí cloud.
* Setup infra (server, storage, network).

**Tech phải biết:** AWS/GCP. **Nên biết:** Terraform.

---

## 4. SECURITY (Security Engineer)
* **Trách nhiệm:** Tìm lỗ hổng hệ thống, test bảo mật (pentest), đề xuất fix security issue.
* 👉 **Thực tế:** Làm việc giống hacker “hợp pháp”.
* **Tech:** Network, Security basics. Nên biết OWASP, Pentest tools.

---

## 5. BLOCKCHAIN (Blockchain Developer)
* **Trách nhiệm:** Viết smart contract, xây backend tương tác blockchain, audit contract.
* 👉 **Thực tế:** Bug = mất tiền thật.
* **Tech:** Solidity / Rust. Nên biết Web3, DeFi.

---

## 6. QA / TESTING (QA Engineer)
* **Trách nhiệm:** Test feature trước khi release, viết test case, report bug.
* 👉 **Thực tế:** Là “người phá sản phẩm”.
* **Tech:** Testing basics. Nên biết Automation.

---

## 7. SYSTEM / NETWORK
* **System Administrator:** Quản lý server, backup dữ liệu, setup môi trường. (Tech: Linux).
* **Network Engineer:** Thiết kế mạng, xử lý sự cố mạng. (Tech: TCP/IP).

---

## 8. MANAGEMENT
* **Product Manager:** Quyết định build cái gì, làm việc với dev + business, viết requirement.
* **Project Manager:** Quản lý timeline, đảm bảo project đúng tiến độ.

---

## TỔNG KẾT THỰC TẾ
* **Backend:** Dễ bắt đầu, nền tảng tốt.
* **Data:** Cần logic + toán.
* **DevOps:** Khó nhưng lương cao.
* **Blockchain:** Rủi ro cao nhưng reward lớn.