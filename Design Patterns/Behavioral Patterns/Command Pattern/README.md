# 📜 Behavioral Patterns: Command

## 1. Định nghĩa
**Command Pattern** là một pattern giúp đóng gói một yêu cầu (request) thành một đối tượng độc lập. Việc đóng gói này cho phép bạn tham số hóa các đối tượng với các yêu cầu khác nhau, xếp hàng (queue), lưu trữ log, hoặc hỗ trợ các thao tác có thể hủy bỏ (undo).

* **Nói đơn giản:** Biến một hành động thành một "phiếu yêu cầu" để có thể chuyển đi, cất giữ hoặc thực hiện sau.



## 2. Vấn đề giải quyết
**❌ Code Smell: Khớp nối cứng (High Coupling)**
```python
class Button:
    def click(self):
        light.turn_on() # Button dính chặt vào Light
```
* **Vấn đề:** Lớp `Button` phụ thuộc trực tiếp vào `Light`. Bạn không thể tái sử dụng `Button` này cho các hành động khác (như mở TV, chạy Bot) mà không sửa code. Cực kỳ khó để mở rộng các tính năng như ghi log hành động hay hoãn thi hành.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Đối tượng gọi trực tiếp hàm xử lý của đối tượng khác.
* **Ta sẽ:** Tạo ra một lớp trung gian (Command) đóng gói toàn bộ thông tin cần thiết để thực hiện hành động đó.

## 4. Cấu trúc (Structure)
1.  **Command (Interface):** Khai báo phương thức `execute()`.
2.  **ConcreteCommand:** Thực thi `execute()` bằng cách gọi các hành động tương ứng trên Receiver.
3.  **Receiver (Người thực hiện):** Chứa logic thực tế (ví dụ: `Light`, `Database`, `TradingAccount`).
4.  **Invoker (Người gọi):** Yêu cầu Command thực hiện hành động (ví dụ: `Button`, `RemoteControl`).

## 5. Implement trong Python

### Bước 1: Command Interface
```python
from abc import ABC, abstractmethod

class Command(ABC):
    @abstractmethod
    def execute(self):
        pass
```

### Bước 2: Receiver (Thằng thực thi logic)
```python
class Light:
    def turn_on(self):
        print("Light is ON")

    def turn_off(self):
        print("Light is OFF")
```

### Bước 3: Concrete Commands
```python
class TurnOnCommand(Command):
    def __init__(self, light: Light):
        self.light = light

    def execute(self):
        self.light.turn_on()

class TurnOffCommand(Command):
    def __init__(self, light: Light):
        self.light = light

    def execute(self):
        self.light.turn_off()
```

### Bước 4: Invoker (Người bấm nút)
```python
class RemoteControl:
    def __init__(self, command: Command):
        self.command = command

    def press(self):
        self.command.execute()
```

## 6. Version thực chiến (Task Queue Mindset) 🏆
Trong hệ thống Backend, Command thường được đẩy vào hàng đợi để xử lý bất đồng bộ (Async).
```python
queue = []
queue.append(TurnOnCommand(light))
queue.append(TurnOffCommand(light))

# Worker xử lý lần lượt
for cmd in queue:
    cmd.execute()
```

## 7. Use case thực tế
* **Job Queue / Background Task:** Các task trong Celery, Redis Queue thực chất là các Command được serialize và gửi đi.
* **Trading System:** * `BuyCommand`, `SellCommand`, `CancelOrderCommand`.
    * Giúp quản lý danh sách lệnh đang chờ (Order Book) và thực thi khi đủ điều kiện.
* **Undo / Redo:** Lưu trữ lịch sử các Command đã chạy để gọi hàm `undo()` ngược lại.
* **Macro / Automation:** Ghi lại (record) một chuỗi các Command và phát lại (replay) chúng.

## 8. Khi nào nên dùng?
* ✅ Khi cần tham số hóa các đối tượng theo hành động.
* ✅ Khi cần xếp hàng tác vụ (queueing), lập lịch thực hiện (scheduling).
* ✅ Khi cần tính năng Undo/Redo hoặc lưu log lịch sử thao tác.

## 9. Khi nào KHÔNG nên dùng?
* ❌ Khi luồng gọi hàm đơn giản và trực tiếp.
* ❌ Tránh làm phức tạp hóa code nếu không cần các tính năng như queue hay hoãn thực thi.

## 10. Best Practices
* ✅ **Nhỏ và gọn:** Mỗi Command chỉ nên làm một việc duy nhất.
* ✅ **Tách biệt Receiver:** Command không nên chứa Business Logic, nó chỉ nên điều hướng yêu cầu đến đúng Receiver.
* ✅ **Metadata:** Nên lưu thêm thông tin như `timestamp`, `user_id` vào Command để dễ dàng debug/log.

## 11. Mental Model
> **Command không phải là một wrapper cho function.** Nó là cách **biến một hành động thành một đối tượng** để bạn có thể kiểm soát toàn bộ vòng đời (lifecycle) của hành động đó (từ lúc tạo ra, xếp hàng cho đến lúc thực thi hoặc hủy bỏ).

## 12. Insight thực chiến cho Hệ thống lớn 🔥
Một hệ thống hiện đại thường kết hợp các pattern theo luồng sau:
1.  **Observer** phát hiện sự kiện (ví dụ: Giá đạt mục tiêu).
2.  **Strategy** quyết định nên làm gì (ví dụ: Nên mua hay bán).
3.  **Command** đóng gói quyết định đó thành một object.
4.  **Queue** tiếp nhận Command để Worker thực thi bất đồng bộ nhằm đảm bảo hiệu năng.

## 13. Tóm tắt 1 dòng
**Command đóng gói "yêu cầu" thành "đối tượng", giúp tách biệt người yêu cầu hành động và người thực hiện hành động.**