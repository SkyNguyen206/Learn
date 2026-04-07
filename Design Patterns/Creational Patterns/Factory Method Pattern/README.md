# 🏭 Creational Patterns: Factory Method

## 1. Định nghĩa
**Factory Method** là pattern dùng để khởi tạo đối tượng mà không cần chỉ định chính xác class cụ thể nào sẽ được tạo. Việc khởi tạo được thực hiện thông qua một method trung gian (Factory).

* **Nói đơn giản:** Không dùng `new` (hoặc khởi tạo trực tiếp) trong code xử lý logic, mà gọi Factory để lấy object.



## 2. Vấn đề giải quyết
**❌ Code Smell phổ biến (Vi phạm nguyên tắc thiết kế):**
```python
def create_payment(method):
    if method == "momo":
        return MomoPayment()
    elif method == "zalopay":
        return ZaloPayPayment()
    elif method == "vnpay":
        return VNPayPayment()
```
* **Vấn đề:** Vi phạm **Open/Closed Principle** (Đóng với việc sửa đổi, mở với việc mở rộng). Mỗi khi thêm phương thức thanh toán mới, bạn buộc phải sửa code cũ, khiến file phình to và dễ gây lỗi.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Dùng `if/else` hoặc `switch-case` dàn trải.
* **Ta sẽ:** Định nghĩa một **Interface** chung, các class cụ thể tự implement logic, và Factory sẽ quyết định tạo object nào dựa trên tham số đầu vào.

## 4. Cấu trúc (Structure)
1.  **Product:** Interface hoặc Abstract Class định nghĩa các phương thức chung.
2.  **Concrete Product:** Các class thực thi (implement) Product.
3.  **Creator (Factory):** Khai báo Factory Method để trả về object kiểu Product.
4.  **Concrete Creator:** Ghi đè Factory Method để trả về thực thể cụ thể.

## 5. Implement trong Python

### Bước 1: Định nghĩa Interface
```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass
```

### Bước 2: Các Concrete Classes
```python
class MomoPayment(Payment):
    def pay(self, amount):
        print(f"Paying {amount} via Momo")

class ZaloPayment(Payment):
    def pay(self, amount):
        print(f"Paying {amount} via ZaloPay")
```

### Bước 3: Factory cơ bản
```python
class PaymentFactory:
    @staticmethod
    def create(method: str) -> Payment:
        if method == "momo":
            return MomoPayment()
        elif method == "zalo":
            return ZaloPayment()
        raise ValueError("Unsupported payment method")
```

## 6. Phiên bản nâng cao (Dùng Registry) 🏆
Đây là cách tiếp cận phổ biến trong môi trường Production để loại bỏ hoàn toàn `if/else`.

```python
class PaymentFactory:
    _registry = {}

    @classmethod
    def register(cls, key, payment_class):
        cls._registry[key] = payment_class

    @classmethod
    def create(cls, key) -> Payment:
        if key not in cls._registry:
            raise ValueError(f"Method {key} not supported")
        return cls._registry[key]()

# Đăng ký các phương thức
PaymentFactory.register("momo", MomoPayment)
PaymentFactory.register("zalo", ZaloPayment)
```

## 7. Use case thực tế
* **Backend API:** Dựa vào `request_type` để tạo ra Handler tương ứng.
* **Trading System:** `strategy = StrategyFactory.create("mean_reversion")`.
* **Data Pipeline:** `parser = ParserFactory.create(file_type)` (JSON, CSV, Parquet).
* **Infrastructure:** `storage = StorageFactory.create("s3")` hoặc `"local"`.

## 8. So sánh nhanh

| Cách tiếp cận | Ưu điểm | Nhược điểm |
| :--- | :--- | :--- |
| **if/else trực tiếp** | Nhanh, đơn giản | Khó maintain, vi phạm OCP |
| **Basic Factory** | Tách biệt logic tạo object | Vẫn còn if/else bên trong Factory |
| **Registry Factory** | Scalable (mở rộng vô hạn) | Setup ban đầu phức tạp hơn |

## 9. Khi nào nên dùng?
* Khi có nhiều loại object cùng chung một interface.
* Khi logic tạo object phức tạp hoặc cần được đóng gói riêng.
* Khi hệ thống cần mở rộng thêm các loại đối tượng mới liên tục.

## 10. Khi nào KHÔNG nên dùng?
* Khi chỉ có 1-2 loại object cố định.
* Khi logic khởi tạo cực kỳ đơn giản (Tránh **Over-engineering**).

## 11. Best Practices
* **Kết hợp:** Thường đi đôi với **Strategy Pattern** và **Dependency Injection**.
* **Tránh:** Để Factory chứa quá nhiều logic nghiệp vụ (Business Logic). Factory chỉ nên làm nhiệm vụ "khởi tạo".

## 12. Mental Model
> **Factory không phải là:** "Tạo object cho đẹp".
> **Factory là:** "Tách biệt việc **tạo** đối tượng ra khỏi việc **sử dụng** đối tượng".

## 13. Insight thực chiến 🔥
Trong thực tế, **80% trường hợp Factory và Strategy sẽ đi cùng nhau**.
1. Dùng **Factory** để lấy ra đúng chiến thuật (Strategy).
2. Gọi hàm thực thi của chiến thuật đó.
```python
strategy = StrategyFactory.create("scalping")
strategy.execute()
```

## 14. Tóm tắt 1 dòng
**Factory giúp loại bỏ `if/else` khi tạo object và giúp hệ thống mở rộng linh hoạt hơn.**

---