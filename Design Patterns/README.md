# 🚀 Design Patterns Quick Guide

Tài liệu tóm tắt các Design Patterns phổ biến, mục đích sử dụng và các nguyên tắc thiết kế quan trọng trong phát triển phần mềm, đặc biệt hữu ích cho Backend và Trading Systems.

---

## 🏗️ 1. Creational Patterns (Khởi tạo đối tượng)

Tập trung vào việc tạo ra các object một cách linh hoạt và tối ưu nhất.

### 🔹 Singleton
* **Mục đích:** Đảm bảo một class chỉ có duy nhất một instance trong suốt vòng đời ứng dụng.
* **Use case:** Database Connection, Config Loader, Logger.
* **⚠️ Lưu ý:** Dễ bị lạm dụng dẫn đến khó viết Unit Test và gây Tight Coupling.

### 🔹 Factory Method
* **Mục đích:** Khởi tạo object mà không cần chỉ định chính xác class cụ thể sẽ được tạo.
* **Use case:** API Response Parsing, khởi tạo Strategy dựa trên cấu hình.
* **Dấu hiệu:** Nếu code có quá nhiều `if type == "A": return A()`, đó là lúc cần dùng Factory.

### 🔹 Dependency Injection (DI)
* **Mục đích:** Truyền các dependency (phụ thuộc) từ bên ngoài vào thay vì khởi tạo bên trong class.
* **Lợi ích:** Code modular hơn, cực kỳ dễ dàng khi Mock dữ liệu để Testing.

---

## 🧩 2. Structural Patterns (Cấu trúc & Tổ chức)

Tập trung vào việc thiết lập mối quan hệ giữa các đối tượng để tạo nên hệ thống lớn hơn.

### 🔹 Adapter
* **Mục đích:** "Bộ chuyển đổi" giúp các interface không tương thích có thể làm việc cùng nhau.
* **Use case:** Tích hợp thư viện bên thứ ba có API không khớp với hệ thống hiện tại, Wrap API cũ.

### 🔹 Decorator
* **Mục đích:** Mở rộng tính năng của đối tượng mà không làm thay đổi code gốc.
* **Thực tế:** Cực kỳ phổ biến trong Python qua các `@decorator` cho Logging, Caching, Auth Middleware.

### 🔹 Facade
* **Mục đích:** Cung cấp một giao diện đơn giản cho một hệ thống con (subsystem) phức tạp.
* **Ví dụ:** Một `PaymentFacade` che giấu các bước phức tạp như `check_balance`, `call_bank_api`, `log_transaction`.

---

## ⚙️ 3. Behavioral Patterns (Hành vi & Luồng xử lý)

Tập trung vào việc giao tiếp và phân chia trách nhiệm giữa các đối tượng.

### 🔹 Strategy
* **Mục đích:** Cho phép thay đổi thuật toán thực thi tại thời điểm runtime.
* **Ứng dụng Trading:** Thay đổi giữa các chiến thuật (EMA, RSI, Arbitrage) một cách linh hoạt.

### 🔹 Observer
* **Mục đích:** Cơ chế Publish-Subscribe, một đối tượng thay đổi sẽ thông báo cho tất cả các thành phần đăng ký nó.
* **Use case:** Event System, WebSocket, Real-time Notification, Message Queue (Kafka).

### 🔹 Command
* **Mục đích:** Đóng gói một yêu cầu thành một đối tượng độc lập.
* **Use case:** Chức năng Undo/Redo, Job Queue, Task Scheduling.

### 🔹 Template Method
* **Mục đích:** Định nghĩa khung (skeleton) của một thuật toán và cho phép các subclass ghi đè (override) từng bước cụ thể.

---

## 💎 4. Những nguyên tắc quan trọng hơn cả Pattern

Việc áp dụng Pattern một cách máy móc sẽ phản tác dụng nếu thiếu các tư duy nền tảng:

* **SOLID Principles:** 5 nguyên tắc vàng (SRP, OCP, LSP, ISP, DIP) để code luôn bền vững.
* **Clean Architecture:** Tách biệt rõ ràng giữa Controller → Service → Repository.
* **Code Smell Awareness:** Nhạy bén với dấu hiệu code xấu (quá nhiều if/else, class quá to, phụ thuộc chồng chéo).

---

## 📈 5. Lộ trình cho Backend & Trading

Nếu bạn định hướng làm **Quant Trading** hoặc **Backend Engineer**, hãy ưu tiên nắm chắc:
1.  **Strategy:** Xử lý logic giao dịch.
2.  **Observer:** Hệ thống khớp lệnh và dữ liệu real-time.
3.  **Factory + DI:** Xây dựng kiến trúc backend linh hoạt.
4.  **Decorator:** Xử lý Middleware và Logging.
5.  **Facade:** Xây dựng API Layer tinh gọn.

---

## 💡 Tư duy đúng (Mindset)
> **Pattern không phải là công thức nấu ăn.** > Đừng cố ép code vào pattern. Hãy coi Pattern là những giải pháp đã được chứng minh cho các vấn đề thường gặp (recurring problems). Chỉ dùng khi thực sự thấy vấn đề cần giải quyết.