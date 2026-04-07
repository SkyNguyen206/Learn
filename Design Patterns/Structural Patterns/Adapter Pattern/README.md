# 🔌 Structural Patterns: Adapter

## 1. Định nghĩa
**Adapter Pattern** là một pattern đóng vai trò là bộ chuyển đổi giữa hai interface không tương thích. Nó cho phép các class có interface khác nhau có thể làm việc cùng nhau bằng cách "bọc" (wrap) interface của class có sẵn thành interface mà client mong muốn.

* **Nói đơn giản:** Giống như một cái đầu chuyển (adapter) giúp bạn cắm phích cắm 3 chân vào ổ điện 2 chân.



## 2. Vấn đề giải quyết
**❌ Code Smell: Interface không tương thích (Mismatch Interface)**
```python
class OldPayment:
    def pay_money(self, amount): # Interface cũ
        print("Old payment system")

# Nhưng hệ thống hiện tại của bạn yêu cầu:
class Payment:
    def pay(self, amount):
        pass
```
* **Vấn đề:** Bạn không thể truyền `OldPayment` vào hệ thống mới vì tên hàm và cấu trúc khác nhau. Bạn cũng không muốn sửa code của `OldPayment` (vì nó là thư viện ngoài hoặc code cũ đang chạy ổn định).

## 3. Ý tưởng cốt lõi
* **Không sửa code cũ** (Open/Closed Principle).
* **Không sửa hệ thống hiện tại.**
* **Giải pháp:** Tạo ra một lớp trung gian (Adapter) đóng vai trò "thông dịch viên" giữa hai bên.

## 4. Cấu trúc (Structure)
1.  **Target:** Interface mà hệ thống hiện tại đang sử dụng.
2.  **Adaptee:** Lớp có sẵn nhưng có interface không tương thích (cần được chuyển đổi).
3.  **Adapter:** Lớp thực thi Target interface và chứa một thực thể của Adaptee để thực hiện việc chuyển đổi.

## 5. Implement trong Python

### Bước 1: Target Interface (Thứ bạn muốn)
```python
from abc import ABC, abstractmethod

class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass
```

### Bước 2: Adaptee (Thứ bạn có - External/Legacy)
```python
class OldPayment:
    def pay_money(self, amount):
        print(f"Old system processing {amount}")
```

### Bước 3: Adapter (Bộ chuyển đổi)
```python
class PaymentAdapter(Payment):
    def __init__(self, old_payment: OldPayment):
        self.old_payment = old_payment

    def pay(self, amount):
        # Chuyển đổi gọi hàm từ pay() sang pay_money()
        self.old_payment.pay_money(amount)
```

## 6. Version thực chiến: Tích hợp Sàn giao dịch (Trading) 🏆
Khi làm Trading Bot, mỗi sàn (Binance, Bybit) có tên hàm khác nhau. Adapter giúp bạn thống nhất chúng:

```python
class BinanceAdapter:
    def buy(self, symbol, qty):
        self.api.create_limit_order(symbol, "BUY", qty)

class BybitAdapter:
    def buy(self, symbol, qty):
        self.api.place_active_order(symbol, "Buy", qty)

# System của bạn chỉ cần gọi:
exchange.buy("BTCUSDT", 1.0)
```

## 7. Use case thực tế
* **Trading System:** Thống nhất API của nhiều sàn giao dịch khác nhau về một chuẩn chung.
* **Backend:** Tích hợp các SDK bên thứ ba (Stripe, PayPal, SendGrid).
* **Data Engineering:** Đọc nhiều định dạng dữ liệu khác nhau (CSV, JSON, XML) qua một interface `DataReader`.
* **Legacy Migration:** Sử dụng lại các module cũ trong kiến trúc mới mà không cần refactor toàn bộ.

## 8. Khi nào nên dùng?
* ✅ Khi muốn sử dụng một class có sẵn nhưng interface của nó không khớp với phần còn lại của code.
* ✅ Khi tạo một thư viện có thể tái sử dụng để làm việc với nhiều hệ thống khác nhau có interface thay đổi.

## 9. Khi nào KHÔNG nên dùng?
* ❌ Khi bạn có quyền sở hữu và có thể sửa đổi cả hai interface một cách dễ dàng.
* ❌ Tránh lạm dụng nếu chỉ có 1-2 chỗ cần mapping đơn giản; tránh làm phức tạp hóa cấu trúc (**Over-engineering**).

## 10. So sánh với các Structural Patterns khác

| Pattern | Vai trò chính |
| :--- | :--- |
| **Adapter** | **Thay đổi** interface của một đối tượng có sẵn. |
| **Decorator** | **Mở rộng** tính năng mà không đổi interface. |
| **Facade** | **Đơn giản hóa** interface của một hệ thống phức tạp. |

## 11. Best Practices
* ✅ **Hạn chế logic:** Adapter chỉ nên làm nhiệm vụ chuyển đổi interface, không nên chứa logic nghiệp vụ (Business Logic).
* ✅ **Đặt tên rõ ràng:** Nên đặt tên kết thúc bằng `Adapter` (ví dụ: `BinanceAdapter`) để người sau dễ bảo trì.

## 12. Mental Model
> **Adapter không phải là một Wrapper bình thường.** Nó là một **Translator (Thông dịch viên)** giúp hai hệ thống không nói cùng một ngôn ngữ có thể hiểu nhau.

## 13. Insight thực chiến 🔥
Trong các hệ thống thực tế, Adapter thường là lớp đầu tiên tiếp xúc với thế giới bên ngoài:
`External API → Adapter → Internal System (Unified Interface)`

**Combo mạnh nhất:**
1. **Factory** tạo ra đúng **Adapter** cho sàn giao dịch.
2. **Adapter** thống nhất interface.
3. **Strategy** xử lý logic trading dựa trên interface đã thống nhất.

## 14. Tóm tắt 1 dòng
**Adapter giúp tích hợp các thành phần không tương thích vào hệ thống mà không cần can thiệp vào mã nguồn gốc của chúng.**