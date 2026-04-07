# 📌 Creational Patterns: Singleton

## 1. Định nghĩa
**Singleton** đảm bảo rằng một class chỉ có **duy nhất 1 instance** trong toàn bộ vòng đời của chương trình và cung cấp một điểm truy cập toàn cục (global access point) đến instance đó.

## 2. Khi nào nên dùng (Use cases thực tế)
Sử dụng cho các tài nguyên chia sẻ hoặc trạng thái toàn cục (**Global State**):
* **Database connection:** Tránh tạo quá nhiều kết nối gây lãng phí tài nguyên.
* **Config loader:** Đảm bảo toàn bộ ứng dụng dùng chung một bộ cài đặt (env, settings).
* **Logger:** Ghi log tập trung vào một đầu mối.
* **Cache manager:** Quản lý bộ nhớ đệm dùng chung.
* **Thread/Connection Pool:** Quản lý số lượng tiến trình hoặc kết nối giới hạn.

> **Ví dụ Backend:** FastAPI app chỉ có một đối tượng config; Database engine chỉ khởi tạo một lần duy nhất.

## 3. Khi nào KHÔNG nên dùng ⚠️
* Khi object có trạng thái (state) thay đổi liên tục theo từng request.
* Khi cần viết Unit Test dễ dàng (vì Singleton gây khó khăn cho việc Mocking).
* Khi có thể sử dụng **Dependency Injection (DI)** để quản lý phụ thuộc tốt hơn.

> **Anti-pattern phổ biến:** Dùng Singleton cho Service hoặc Business Logic → gây ra hiện tượng **Tight Coupling** (phụ thuộc quá chặt chẽ).

## 4. Cách Implement trong Python

### Cách 1: Dùng `__new__` (Classic)
```python
class Singleton:
    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance
```
* **Ưu điểm:** Đơn giản, dễ hiểu theo tư duy hướng đối tượng truyền thống.

### Cách 2: Dùng Decorator (Clean hơn)
```python
def singleton(cls):
    instances = {}

    def wrapper(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]

    return wrapper

@singleton
class Config:
    pass
```

### Cách 3: Dùng Module (Pythonic nhất) 🏆
Trong Python, bản chất mỗi module khi được import đã là một Singleton.
```python
# config.py
class Config:
    def __init__(self):
        self.value = 123

config = Config() # Khởi tạo ngay tại module

# main.py
from config import config # Import và sử dụng instance đã khởi tạo
```
* **Thực tế:** Đây là cách được tin dùng nhất trong các dự án Production.

## 5. Vấn đề thường gặp
* **Hidden Global State:** Làm code trở nên khó hiểu và khó debug do trạng thái có thể bị thay đổi từ bất cứ đâu.
* **Khó Test:** Không thể reset instance giữa các lần chạy test case khác nhau.
* **Race Condition:** Trong môi trường đa luồng (multi-thread), nếu không có cơ chế `Lock`, có thể dẫn đến việc tạo nhiều instance cùng lúc.

## 6. Best Practices
* **Ưu tiên:** Sử dụng **Dependency Injection** thay vì Singleton nếu có thể.
* **Chỉ dùng Singleton cho:** Resource Manager (DB, Cache, Config).
* **Tránh dùng cho:** Business Logic.
* **Trong FastAPI:** Hãy tận dụng `Depends()` thay vì gọi trực tiếp Singleton.

## 7. So sánh nhanh

| Tiêu chí | Singleton | Dependency Injection |
| :--- | :--- | :--- |
| **Dễ dùng** | ✅ (Gọi trực tiếp) | ❌ (Cần setup ban đầu) |
| **Khả năng Test** | ❌ (Rất khó) | ✅ (Dễ dàng Mock) |
| **Linh hoạt** | ❌ (Cố định) | ✅ (Dễ thay đổi) |
| **Coupling** | Cao | Thấp |

## 8. Ví dụ thực tế (Backend Mindset)

❌ **Sai (Lạm dụng):**
```python
class UserService(Singleton):
    def get_user(self):
        pass
# Service không nên là Singleton vì chứa logic nghiệp vụ
```

✅ **Đúng (Quản lý tài nguyên):**
```python
# db.py
engine = create_engine(...)

# config.py
settings = Settings()
```

## 9. Mental Model
> **Singleton không phải là:** "Chỉ có 1 object là tốt".
> **Singleton là:** "Việc có đúng 1 instance là **bắt buộc** về mặt logic để đảm bảo tính đúng đắn của hệ thống".

## 10. Checklist trước khi dùng Singleton
1. Có thực sự cần duy nhất 1 instance không?
2. Có cần truy cập toàn cục (global access) không?
3. Việc dùng nó có phá hỏng Unit Test không?
4. Có thể thay thế bằng DI được không?

👉 Nếu bạn có **≥ 2 câu trả lời "Không chắc"** → Đừng dùng Singleton.

## 11. Tóm tắt 1 dòng
**Singleton dùng cho tài nguyên dùng chung toàn hệ thống, tuyệt đối không dùng cho logic nghiệp vụ.**

---