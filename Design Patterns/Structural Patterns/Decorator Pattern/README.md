# 🎀 Structural Patterns: Decorator

## 1. Định nghĩa
**Decorator** là pattern cho phép bạn thêm các hành vi (behavior) hoặc trách nhiệm mới vào một đối tượng một cách linh hoạt (dynamic) mà không làm thay đổi cấu trúc hay mã nguồn gốc của đối tượng đó.

* **Nói đơn giản:** Giống như việc bạn mặc thêm áo khoác. Bạn vẫn là bạn, nhưng bạn có thêm khả năng "chống lạnh".



## 2. Vấn đề giải quyết
**❌ Code Smell: Trộn lẫn logic (Cross-cutting Concerns)**
```python
def get_data():
    print("Logging: Start fetching...") # Logic phụ
    data = fetch_from_db()               # Business Logic chính
    print("Logging: Done.")              # Logic phụ
    return data
```
* **Vấn đề:** Logic chính bị "bao vây" bởi các logic phụ (logging, auth, caching). Điều này khiến code khó tái sử dụng, khó đọc và vi phạm nguyên tắc **Single Responsibility Principle**.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Sửa trực tiếp vào hàm hoặc class gốc.
* **Ta sẽ:** "Bọc" (Wrap) hàm gốc bằng một hàm khác (decorator) để thực hiện các hành động trước và sau khi hàm gốc chạy.

## 4. Implement trong Python (Function-based)

### Cách 1: Cơ bản
```python
def logger(func):
    def wrapper(*args, **kwargs):
        print("Start")
        result = func(*args, **kwargs)
        print("End")
        return result
    return wrapper

@logger
def get_data():
    print("Fetching data...")
```

### Cách 2: Chuẩn Production (Dùng `wraps`) 🏆
Khi dùng decorator, metadata của hàm gốc (như tên hàm, docstring) sẽ bị mất. Ta cần `functools.wraps` để giữ lại chúng.
```python
from functools import wraps

def logger(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
```

## 5. Decorator có tham số
Dùng khi bạn muốn cấu hình hành vi của decorator (ví dụ: số lần retry, level của log).
```python
def repeat(n):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def say_hi():
    print("Hi!")
```

## 6. Use case thực tế (Cực kỳ quan trọng)
* **Authorization / Permissions:** Kiểm tra user có quyền truy cập không trước khi vào hàm xử lý.
* **Caching:** Kiểm tra dữ liệu trong Redis/Memcached trước khi tính toán nặng (`@lru_cache`).
* **Logging:** Tự động ghi lại thời gian thực thi và tham số đầu vào của mọi API.
* **Rate Limiting:** Giới hạn số lần gọi hàm trong một khoảng thời gian.
* **Retry Logic:** Tự động gọi lại hàm nếu xảy ra lỗi mạng.

## 7. So sánh nhanh

| Pattern | Vai trò chính |
| :--- | :--- |
| **Decorator** | **Thêm behavior** mà không đổi interface. |
| **Adapter** | **Thay đổi interface** để tương thích. |
| **Strategy** | **Thay đổi logic** cốt lõi bên trong. |

## 8. Khi nào nên dùng?
* ✅ Khi muốn thêm tính năng cho đối tượng tại runtime mà không ảnh hưởng đến các đối tượng khác cùng lớp.
* ✅ Khi không thể mở rộng đối tượng bằng cách kế thừa (ví dụ: class bị `final` hoặc quá nhiều lớp con gây bùng nổ phân cấp).
* ✅ Khi cần tách biệt các logic bổ trợ (logging, auth) ra khỏi Business Logic.

## 9. Khi nào KHÔNG nên dùng?
* ❌ Khi logic thêm vào quá phức tạp và làm thay đổi hoàn toàn kết quả của hàm gốc (gây khó debug).
* ❌ Tránh việc lạm dụng "chồng" quá nhiều decorator (`Stacking`) khiến luồng dữ liệu trở nên mờ ám.

## 10. Best Practices
* ✅ **Luôn dùng `@wraps`:** Để đảm bảo metadata của hàm không bị mất.
* ✅ **Đơn giản hóa:** Mỗi decorator chỉ nên làm một nhiệm vụ duy nhất.
* ✅ **Thứ tự quan trọng:** Decorator nằm gần hàm nhất sẽ được thực thi trước.
  ```python
  @auth  # Chạy sau
  @log   # Chạy trước
  def sensitive_action():
      pass
  ```

## 11. Mental Model
> **Decorator không phải là "syntax cho đẹp".** Nó là một **Wrapper** giúp bạn thực thi logic theo kiểu "củ hành": bọc nhiều lớp hành vi bên ngoài lõi Business Logic mà không làm hỏng cái lõi đó.

## 12. Insight thực chiến 🔥
Trong các hệ thống Backend (FastAPI, Flask, Django), Decorator chính là linh hồn của các bộ lọc:
`Request → Auth Decorator → Rate Limit Decorator → Logging Decorator → [Business Logic] → Response`

**Combo mạnh nhất:**
1. **Decorator** xử lý các vấn đề chung (Auth, Log, Cache).
2. **Strategy** xử lý logic nghiệp vụ biến thiên.
3. **DI** truyền các tài nguyên (DB, Config) vào trong.

## 13. Tóm tắt 1 dòng
**Decorator giúp bạn "trang trí" thêm tính năng cho hàm hoặc class mà vẫn giữ nguyên vẹn mã nguồn gốc.**

---