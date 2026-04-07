# 🏛️ Structural Patterns: Facade

## 1. Định nghĩa
**Facade** là pattern cung cấp một giao diện (interface) đơn giản hóa cho một hệ thống con (subsystem) phức tạp gồm nhiều lớp, thư viện hoặc module bên trong.

* **Nói đơn giản:** Giống như một cái điều khiển từ xa (Remote). Bạn chỉ cần bấm nút "Bật TV", còn việc bên trong TV xử lý nguồn, màn hình, âm thanh như thế nào thì bạn không cần quan tâm.



## 2. Vấn đề giải quyết
**❌ Code Smell: Khớp nối quá chặt (High Coupling)**
```python
def place_order():
    check_inventory()       # Subsystem A
    process_payment()       # Subsystem B
    create_order_record()   # Subsystem C
    send_email()            # Subsystem D
    log_transaction()       # Subsystem E
```
* **Vấn đề:** Client phải biết quá nhiều về các module nội bộ. Nếu trình tự thay đổi hoặc một module bị thay thế, bạn phải sửa code ở tất cả những nơi gọi hàm này. Hệ thống trở nên cực kỳ khó bảo trì.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Để Client tự gọi và điều phối từng module nhỏ.
* **Ta sẽ:** Tạo ra một lớp "mặt tiền" (Facade) chịu trách nhiệm điều phối toàn bộ các module phức tạp đó. Client chỉ cần gọi một phương thức duy nhất từ Facade.

## 4. Cấu trúc (Structure)
1.  **Facade:** Lớp điều hướng, biết rõ module nào cần thực hiện nhiệm vụ gì.
2.  **Subsystems:** Các lớp thực hiện logic chi tiết (Inventory, Payment, v.v.). Chúng không biết đến sự tồn tại của Facade.

## 5. Implement trong Python

### Bước 1: Các Subsystems (Các module phức tạp)
```python
class InventoryService:
    def check(self): print("Checking inventory...")

class PaymentService:
    def pay(self): print("Processing payment...")

class OrderService:
    def create(self): print("Creating order record...")
```

### Bước 2: Facade (Lớp mặt tiền)
```python
class OrderFacade:
    def __init__(self):
        self.inventory = InventoryService()
        self.payment = PaymentService()
        self.order = OrderService()

    def place_order(self):
        # Điều phối luồng xử lý phức tạp tại một nơi duy nhất
        self.inventory.check()
        self.payment.pay()
        self.order.create()
        print("Order placed successfully!")
```

## 6. Version thực chiến: Service Layer (Backend Mindset) 🏆
Trong kiến trúc Clean Architecture, **Service Layer** đóng vai trò chính là một Facade:
```python
class UserService: # Đây là một Facade
    def __init__(self, repo, cache, mailer):
        self.repo = repo
        self.cache = cache
        self.mailer = mailer

    def register_user(self, data):
        user = self.repo.save(data)
        self.cache.update(user)
        self.mailer.send_welcome(user)
        return user
```

## 7. Use case thực tế
* **Backend API:** Controller gọi Service (Facade) thay vì gọi trực tiếp Repository hay các dịch vụ bên ngoài.
* **Microservices:** Một API Gateway đóng vai trò Facade cho hàng loạt các microservices nhỏ bên dưới.
* **Trading System:** `execute_trade()` bọc các bước: Check rủi ro → Chọn chiến thuật → Gửi lệnh sàn → Ghi Log.
* **SDK / Library Wrapper:** Đóng gói một thư viện phức tạp thành các hàm đơn giản cho người dùng (ví dụ: bọc thư viện `requests` để tạo `MyHttpClient`).

## 8. So sánh nhanh

| Pattern | Vai trò chính |
| :--- | :--- |
| **Facade** | **Đơn giản hóa** giao diện của hệ thống phức tạp. |
| **Adapter** | **Chuyển đổi** interface để hai bên hiểu nhau. |
| **Decorator** | **Thêm behavior** cho đối tượng mà không đổi interface. |

## 9. Khi nào nên dùng?
* ✅ Khi bạn muốn cung cấp một interface đơn giản cho một hệ thống con phức tạp.
* ✅ Khi muốn phân lớp (layering) hệ thống: Dùng Facade làm điểm truy cập cho mỗi tầng.
* ✅ Khi muốn giảm sự phụ thuộc giữa Client và các lớp thực thi logic.

## 10. Khi nào KHÔNG nên dùng?
* ❌ Khi hệ thống của bạn rất đơn giản.
* ❌ Tránh biến Facade thành **"God Class"** (một lớp biết tất cả và làm tất cả mọi thứ).

## 11. Best Practices
* ✅ **Orchestration Only:** Facade chỉ nên điều phối các service, không nên chứa logic nghiệp vụ (Business Logic) phức tạp bên trong.
* ✅ **Tách biệt rõ ràng:** Luôn giữ Business Logic ở lớp Subsystem/Repository và chỉ dùng Facade để gọi chúng theo thứ tự.

## 12. Mental Model
> **Facade không phải là "gom code cho gọn".** Nó là việc tạo ra một **Entry Point (Cổng vào)** rõ ràng và duy nhất, giúp người dùng hệ thống không bị lạc lối trong sự phức tạp của các module bên trong.

## 13. Insight thực chiến 🔥
Trong các hệ thống Production chuẩn:
`Controller (Input) → Facade (Service Layer) → Repository (Data Access) → Database`

**Combo mạnh nhất:**
1. **Facade** là cổng vào duy nhất.
2. **DI** truyền các subsystem vào Facade.
3. **Strategy** được Facade chọn để xử lý logic bên trong.
4. **Adapter** giúp Facade giao tiếp với các API bên thứ ba.

## 14. Tóm tắt 1 dòng
**Facade giúp che giấu sự phức tạp của hệ thống và cung cấp một cách sử dụng đơn giản, an toàn cho Client.**

---