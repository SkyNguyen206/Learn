# 💉 Dependency Injection (DI)

## 1. Định nghĩa
**Dependency Injection (DI)** là một kỹ thuật trong lập trình cho phép một đối tượng nhận các phụ thuộc (dependencies) của nó từ bên ngoài truyền vào, thay vì tự khởi tạo chúng bên trong lớp.

* **Hiểu đơn giản:** Đừng tự đi mua nguyên liệu, hãy để người khác mang đến tận bếp.



## 2. Vấn đề giải quyết
**❌ Code Smell: Thắt nút cổ chai (Tight Coupling)**
```python
class UserService:
    def __init__(self):
        self.repo = UserRepository() # Tự khởi tạo bên trong
```
* **Vấn đề:** Phụ thuộc bị "Hard-code". Bạn không thể thay thế `UserRepository` bằng một bản Mock khi viết Test, cũng không thể đổi sang một loại Database khác mà không sửa code của `UserService`.

## 3. Cách tiếp cận đúng (DI)
```python
class UserService:
    def __init__(self, repo):
        self.repo = repo # Nhận từ bên ngoài
```
* **Sử dụng:**
```python
repo = UserRepository()
service = UserService(repo) # Dependency được "tiêm" (inject) vào
```

## 4. Tư duy cốt lõi
* Class **không nên** biết cách tạo ra dependency.
* Class chỉ nên tuyên bố: **"Tôi cần cái này để hoạt động"**.

## 5. Các loại Dependency Injection

| Loại | Cách thực hiện | Mức độ phổ biến |
| :--- | :--- | :--- |
| **Constructor Injection** | Truyền qua hàm `__init__` | **90% trường hợp** |
| **Setter Injection** | Truyền qua một hàm `set_repo()` | Ít dùng |
| **Method Injection** | Truyền trực tiếp vào tham số của hàm xử lý | Dùng cho function-level |

## 6. So sánh: DI vs. Singleton

| Tiêu chí | Dependency Injection | Singleton |
| :--- | :--- | :--- |
| **Coupling** | **Thấp** (Loose Coupling) | **Cao** (Tight Coupling) |
| **Unit Test** | **Dễ** (Dễ dàng Mocking) | **Khó** (Dính chặt vào Global State) |
| **Tính linh hoạt** | **Cao** | **Thấp** |

👉 **Rule:** Ưu tiên dùng DI. Chỉ dùng Singleton khi thực sự bắt buộc về mặt logic hệ thống.

## 7. Use case thực tế

### 🔹 Backend (FastAPI Mindset)
Tách biệt Business Logic và Data Access:
```python
class UserService:
    def __init__(self, repo):
        self.repo = repo
```

### 🔹 Testing (Cực kỳ quan trọng)
Giúp test logic mà không cần Database thật:
```python
class FakeRepo:
    def get_user(self):
        return {"id": 1, "name": "Test User"}

# Inject FakeRepo vào Service để test
service = UserService(FakeRepo())
```

### 🔹 Trading System
Dễ dàng thay đổi chiến thuật giao dịch mà không sửa Engine:
```python
strategy = MeanReversionStrategy()
engine = TradingEngine(strategy)
```

## 8. DI trong FastAPI (Thực chiến)
FastAPI tích hợp sẵn một DI container đơn giản thông qua `Depends`:
```python
from fastapi import Depends

def get_repo():
    return UserRepository()

def get_service(repo = Depends(get_repo)):
    return UserService(repo)
```

## 9. Kết hợp DI với Factory
Sự kết hợp hoàn hảo để quản lý hệ thống phức tạp:
1. **Factory** chịu trách nhiệm **tạo** object phù hợp (ví dụ: Mongo hoặc Postgres).
2. **DI** chịu trách nhiệm **inject** object đó vào hệ thống để sử dụng.

## 10. Dấu hiệu cần áp dụng DI (Code Smells)
* ❌ Class tự tạo đối tượng khác bên trong constructor: `self.db = Database()`.
* ❌ Import trực tiếp instance từ file khác vào giữa logic: `from db import db_instance`.
* ❌ Gặp khó khăn khi viết Unit Test vì phải setup quá nhiều thứ liên quan đến môi trường (DB, Network).

## 11. Best Practices
* ✅ **Luôn** inject qua Constructor.
* ✅ **Dùng Interface (ABC trong Python)** để định nghĩa kiểu dữ liệu cho dependency.
* ⚠️ **Tránh** inject quá nhiều thứ (quá 5-7 dependencies) vào một class → Dấu hiệu class đang làm quá nhiều việc (vi phạm SRP).

## 12. Mental Model
> DI không đơn thuần là "truyền tham số". Nó là **Đảo ngược quyền kiểm soát (Inversion of Control - IoC)**: Class không còn nắm quyền quyết định khởi tạo phụ thuộc, quyền đó thuộc về môi trường thực thi bên ngoài.

## 13. Checklist quyết định
1. Class có đang tự tạo object khác không?
2. Bạn có muốn thay thế implementation (ví dụ đổi từ MySQL sang MongoDB) mà không sửa logic chính không?
3. Bạn có cần Mock dữ liệu để chạy Test nhanh không?

👉 Nếu có bất kỳ câu trả lời **"Có"** → Hãy dùng **Dependency Injection**.

## 14. Tóm tắt 1 dòng
**DI giúp giảm sự phụ thuộc giữa các thành phần, tăng khả năng kiểm thử (testability) và làm code linh hoạt hơn trước mọi thay đổi.**