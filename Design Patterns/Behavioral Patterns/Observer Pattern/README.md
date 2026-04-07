# 🔔 Behavioral Patterns: Observer

## 1. Định nghĩa
**Observer** (hay còn gọi là **Publish-Subscribe**) là một pattern cho phép một đối tượng (gọi là **Subject**) tự động thông báo và cập nhật trạng thái cho tất cả các đối tượng đăng ký theo dõi nó (gọi là **Observers**) khi có bất kỳ sự thay đổi nào xảy ra.



## 2. Vấn đề giải quyết
**❌ Code Smell: Thắt nút cổ chai (Tight Coupling)**
```python
class TradingEngine:
    def on_price_update(self, price):
        self.update_ui(price)
        self.save_db(price)
        self.send_notification(price)
```
* **Vấn đề:** Lớp `TradingEngine` phải biết quá nhiều module khác. Mỗi khi muốn thêm một hành động mới (ví dụ: Gửi email), bạn buộc phải sửa code của engine này → Vi phạm nguyên tắc **Open/Closed**.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Subject đi gọi trực tiếp từng function của các module khác.
* **Ta sẽ:** Subject chỉ việc "phát sóng" (publish) một sự kiện, các module khác (Observers) tự đăng ký (subscribe) để lắng nghe và xử lý theo cách riêng của chúng.

## 4. Cấu trúc (Structure)
1.  **Subject (Publisher):** Quản lý danh sách các Observer, cung cấp phương thức `subscribe()`, `unsubscribe()` và `notify()`.
2.  **Observer (Subscriber):** Interface định nghĩa phương thức `update()` để nhận thông báo.
3.  **Concrete Observers:** Các lớp thực thi cụ thể (ví dụ: `Logger`, `EmailService`).

## 5. Implement trong Python

### Bước 1: Interface cho Observer
```python
from abc import ABC, abstractmethod

class Observer(ABC):
    @abstractmethod
    def update(self, data):
        pass
```

### Bước 2: Subject (Lớp phát tín hiệu)
```python
class Subject:
    def __init__(self):
        self._observers = []

    def subscribe(self, observer: Observer):
        self._observers.append(observer)

    def unsubscribe(self, observer: Observer):
        self._observers.remove(observer)

    def notify(self, data):
        for obs in self._observers:
            obs.update(data)
```

### Bước 3: Các Concrete Observers
```python
class Logger(Observer):
    def update(self, data):
        print(f"[LOG]: {data}")

class DatabaseSaver(Observer):
    def update(self, data):
        print(f"[DB]: Saving {data} to database")
```

## 6. Phiên bản thực chiến (Event-Driven) 🏆
Trong thực tế, thay vì truyền dữ liệu thô, người ta thường truyền một **Event Object** để mang theo nhiều thông tin ngữ cảnh hơn.

```python
class PriceEvent:
    def __init__(self, symbol: str, price: float):
        self.symbol = symbol
        self.price = price

# Khi notify:
event = PriceEvent("BTC", 65000)
subject.notify(event)
```

## 7. Use case thực tế
* **Trading System (Cực kỳ quan trọng):**
    * Dữ liệu thị trường (Market Data) thay đổi → Cập nhật cho: `Strategy` (tính toán), `RiskManager` (kiểm tra rủi ro), `UI` (hiển thị đồ thị).
* **Backend Real-time:** WebSocket broadcast dữ liệu cho người dùng.
* **Hệ thống thông báo:** Gửi đồng thời Email, SMS, Push Notification khi có đơn hàng mới.
* **Message Queue:** Các hệ thống như Kafka, RabbitMQ thực chất là Observer pattern ở mức kiến trúc hệ thống (Async).

## 8. Khi nào nên dùng?
* ✅ Khi một thay đổi ở đối tượng này đòi hỏi thay đổi ở các đối tượng khác mà không biết trước có bao nhiêu đối tượng cần thay đổi.
* ✅ Khi muốn tách biệt (decouple) giữa bên phát tin và bên nhận tin.

## 9. Khi nào KHÔNG nên dùng?
* ❌ Khi luồng xử lý đơn giản, chỉ có 1-2 hành động cố định.
* ❌ Tránh dùng nếu danh sách Observer quá lớn và các thao tác xử lý trong Observer quá nặng, gây nghẽn Subject (nên chuyển sang Async/Queue).

## 10. Best Practices
* ✅ **Dùng Event Object:** Đóng gói dữ liệu vào class thay vì truyền raw string/int.
* ✅ **Tính độc lập:** Các Observer không nên phụ thuộc hoặc gọi lẫn nhau.
* ✅ **Async:** Sử dụng `asyncio` hoặc hàng đợi nếu việc xử lý trong Observer tốn thời gian.

## 11. Mental Model
> **Observer không phải là một callback đơn giản.** Nó là một **Event System** giúp các thành phần trong hệ thống giao tiếp với nhau mà không cần biết "đối phương" là ai.

## 12. Insight thực chiến cho Trading 🔥
Hầu hết các hệ thống giao dịch hiện đại đều vận hành theo luồng:
1. **Dữ liệu vào** (Event).
2. **Observer** nhận tín hiệu.
3. **Strategy** (kết hợp Strategy Pattern) xử lý logic.
4. **Order Factory** tạo lệnh.
5. **DI** tiêm các phụ thuộc cần thiết.

```python
# Luồng sát thực tế:
event = PriceEvent("BTC", 65000)
subject.notify(event)

# Trong StrategyObserver.update():
if strategy.should_buy(event.price):
    order = OrderFactory.create("market_buy")
    broker.execute(order)
```

## 13. Tóm tắt 1 dòng
**Observer giúp hệ thống phản ứng linh hoạt với các sự kiện mà không làm rối loạn cấu trúc code chính.**

---