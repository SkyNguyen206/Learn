# 📈 Behavioral Patterns: Strategy

## 1. Định nghĩa
**Strategy Pattern** cho phép định nghĩa một tập hợp các thuật toán, đóng gói từng thuật toán lại và làm cho chúng có thể hoán đổi cho nhau linh hoạt tại thời điểm thực thi (runtime).

* **Nói đơn giản:** Biến các hành vi (behavior) thành các đối tượng có thể thay thế được.



## 2. Vấn đề giải quyết
**❌ Code Smell: "Rừng" if/else hoặc switch-case**
```python
def calculate(price, type):
    if type == "discount":
        return price * 0.9
    elif type == "tax":
        return price * 1.1
    elif type == "vip":
        return price * 0.8
```
* **Vấn đề:** Khi có thêm loại thuế hoặc mã giảm giá mới, file này sẽ phình to (Fat Function), cực kỳ khó Unit Test từng logic riêng biệt và vi phạm nguyên tắc Open/Closed.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Viết tất cả logic vào một hàm khổng lồ với nhiều nhánh rẽ.
* **Ta sẽ:** Tách mỗi thuật toán/logic xử lý thành một class riêng biệt và chọn "chiến thuật" phù hợp để truyền vào đối tượng sử dụng.

## 4. Cấu trúc (Structure)
1.  **Strategy (Interface):** Định nghĩa phương thức chung cho mọi thuật toán.
2.  **Concrete Strategy:** Các lớp thực thi thuật toán cụ thể (ví dụ: `Discount`, `Tax`).
3.  **Context:** Lớp sử dụng Strategy. Nó giữ một tham chiếu đến đối tượng Strategy và gọi hàm thực thi.

## 5. Implement trong Python

### Bước 1: Định nghĩa Interface
```python
from abc import ABC, abstractmethod

class PricingStrategy(ABC):
    @abstractmethod
    def calculate(self, price):
        pass
```

### Bước 2: Các Concrete Strategies
```python
class DiscountStrategy(PricingStrategy):
    def calculate(self, price):
        return price * 0.9

class TaxStrategy(PricingStrategy):
    def calculate(self, price):
        return price * 1.1
```

### Bước 3: Context (Lớp sử dụng)
```python
class PriceCalculator:
    def __init__(self, strategy: PricingStrategy):
        self.strategy = strategy # Inject strategy vào đây

    def execute(self, price):
        return self.strategy.calculate(price)
```

## 6. Phiên bản thực chiến (Kết hợp Factory + DI) 🏆
Trong thực tế, bạn sẽ không khởi tạo thủ công mà kết hợp các pattern lại:
```python
# 1. Dùng Factory để chọn strategy từ cấu hình/user input
strategy = StrategyFactory.create("discount")

# 2. Dùng DI để inject strategy vào engine xử lý
calculator = PriceCalculator(strategy)

# 3. Thực thi
calculator.execute(100)
```

## 7. Use case thực tế
* **Trading System (Cực kỳ quan trọng):** * `MeanReversionStrategy()`, `BreakoutStrategy()`, `ScalpingStrategy()`.
    * Chỉ cần swap object → Toàn bộ bot thay đổi tư duy giao dịch.
* **Payment Processing:** Xử lý thanh toán qua Credit Card, PayPal, Crypto.
* **Routing/Navigation:** Tìm đường đi ngắn nhất (Walking, Driving, Public Transport).

## 8. So sánh nhanh bộ ba Creational - Structural - Behavioral

| Pattern | Vai trò chính |
| :--- | :--- |
| **Factory** | Chịu trách nhiệm **Tạo** object. |
| **DI** | Chịu trách nhiệm **Truyền** object vào nơi cần. |
| **Strategy** | Chịu trách nhiệm **Thực thi** logic của object đó. |

## 9. Dấu hiệu cần dùng Strategy
* ✅ Xuất hiện `if/else` dài dằng dặc dựa trên "loại" (type/mode) của dữ liệu.
* ✅ Một function/class quá dài và chứa quá nhiều business rules khác nhau.
* ✅ Bạn cần thay đổi thuật toán xử lý ngay khi chương trình đang chạy mà không muốn khởi động lại.

## 10. Khi nào KHÔNG nên dùng?
* ❌ Khi chỉ có 1-2 logic cực kỳ đơn giản và ít khi thay đổi. Tránh làm phức tạp hóa vấn đề (**Over-engineering**).

## 11. Best Practices
* ✅ **Tính độc lập:** Mỗi strategy phải nhỏ, gọn và không phụ thuộc vào các strategy khác.
* ✅ **Sự mù quáng của Context:** Lớp Context không được phép biết chi tiết bên trong strategy làm gì, nó chỉ gọi hàm `execute()`.

## 12. Mental Model
> **Strategy không đơn thuần là chia nhỏ code.** Nó là việc biến **hành vi (behavior)** thành một **đối tượng (object)** có thể tháo lắp như các khối lego.

## 13. Insight thực chiến 🔥
**Combo mạnh nhất trong Backend/Trading:**
1. **Factory** → Đọc config và tạo ra đúng Strategy.
2. **DI** → Tiêm Strategy đó vào Trading Engine.
3. **Strategy** → Engine chạy mà không cần quan tâm logic bên dưới là gì.

```python
# Ví dụ full flow:
strategy_name = config.get("CURRENT_STRATEGY") # "mean_reversion"
strategy = StrategyFactory.create(strategy_name)
engine = TradingEngine(strategy)

engine.run() # Hệ thống tự động chạy theo logic mean_reversion
```

## 14. Tóm tắt 1 dòng
**Strategy giúp bạn thay đổi "cách làm" của hệ thống mà không cần sửa đổi code hiện tại.**

---