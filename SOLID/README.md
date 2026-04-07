# 💎 SOLID Principles

**SOLID** là 5 nguyên tắc thiết kế phần mềm giúp code của bạn dễ mở rộng, dễ viết Unit Test và hạn chế tối đa bug khi thay đổi yêu cầu.



## 1. S — Single Responsibility Principle (SRP)
### 📌 Định nghĩa
Một class chỉ nên có **một và chỉ một lý do** để thay đổi. Nghĩa là mỗi class chỉ nên đảm nhận một nhiệm vụ duy nhất.

**❌ Sai (Vi phạm SRP):**
```python
class UserService:
    def create_user(self, data):
        # business logic xử lý user
        pass

    def send_email(self, user):
        # logic gửi email notification
        pass

    def log_error(self, msg):
        # logic ghi log vào file
        pass
```
* **Vấn đề:** Class này làm quá nhiều việc: Xử lý nghiệp vụ, thông báo, và ghi log. Nếu logic gửi email thay đổi, bạn phải sửa `UserService`.

**✅ Đúng:**
Tách nhỏ thành các service chuyên biệt:
```python
class UserService:
    def create_user(self, data): pass

class EmailService:
    def send(self, user): pass

class Logger:
    def log(self, msg): pass
```

### 🧠 Mental Model
> "Nếu một class có thể bị thay đổi vì 2 lý do khác nhau (ví dụ: đổi DB và đổi mẫu Email) → Chắc chắn vi phạm SRP."

* **🔍 Code Smell:** Class dài > 200 dòng, nhiều method không liên quan, tên class quá chung chung (như `Manager`, `Helper`).

---

## 2. O — Open/Closed Principle (OCP)
### 📌 Định nghĩa
Phần mềm nên **mở để mở rộng** (Open for extension) nhưng **đóng để sửa đổi** (Closed for modification).

**❌ Sai:**
```python
def calculate_price(price, type):
    if type == "vip":
        return price * 0.8
    elif type == "normal":
        return price
    # Thêm type mới lại phải vào đây sửa if/else
```

**✅ Đúng (Áp dụng Strategy Pattern):**
```python
class PricingStrategy:
    def apply(self, price): pass

class VipStrategy(PricingStrategy):
    def apply(self, price): return price * 0.8
```

### 🧠 Mental Model
> "Thêm tính năng mới bằng cách viết thêm code mới (class mới), không phải bằng cách sửa code cũ đang chạy ổn định."

* **🔍 Code Smell:** `if/else` quá dài, `switch-case` nhiều nhánh, phải sửa code cũ liên tục mỗi khi có yêu cầu mới.

---

## 3. L — Liskov Substitution Principle (LSP)
### 📌 Định nghĩa
Các đối tượng của class con phải có khả năng **thay thế** được class cha mà không làm thay đổi tính đúng đắn của chương trình.

**❌ Sai:**
```python
class Bird:
    def fly(self): pass

class Penguin(Bird): # Chim cánh cụt là chim nhưng không biết bay
    def fly(self):
        raise Exception("Cannot fly!") 
```
* **Vấn đề:** Nếu một hàm mong đợi một `Bird` và gọi `.fly()`, nó sẽ crash khi nhận vào `Penguin`.

**✅ Đúng:**
Tách interface phù hợp:
```python
class Bird: pass

class FlyingBird(Bird):
    def fly(self): pass

class Penguin(Bird): pass # Chỉ kế thừa Bird, không có fly
```

### 🧠 Mental Model
> "Nếu bạn thay class con vào vị trí class cha mà code bị crash hoặc chạy sai logic → Vi phạm LSP."

---

## 4. I — Interface Segregation Principle (ISP)
### 📌 Định nghĩa
Thay vì một Interface lớn, nên chia thành nhiều **Interface nhỏ**, đúng mục đích. Không nên ép class implement những method mà nó không dùng đến.

**❌ Sai:**
```python
class Worker:
    def work(self): pass
    def eat(self): pass

class Robot(Worker):
    def eat(self):
        pass # Robot không biết ăn, method này vô nghĩa
```

**✅ Đúng:**
```python
class Workable:
    def work(self): pass

class Eatable:
    def eat(self): pass

class Robot(Workable): # Chỉ implement những gì cần
    def work(self): pass
```

### 🧠 Mental Model
> "Interface càng nhỏ gọn càng tốt. Đừng bắt Robot phải biết ăn, đừng bắt Cá phải biết leo cây."

---

## 5. D — Dependency Inversion Principle (DIP)
### 📌 Định nghĩa
1. Các module cấp cao không nên phụ thuộc vào module cấp thấp. Cả hai nên phụ thuộc vào **Abstraction (Interface)**.
2. Abstraction không nên phụ thuộc vào chi tiết, mà chi tiết nên phụ thuộc vào abstraction.

**❌ Sai (Phụ thuộc cứng):**
```python
class UserService:
    def __init__(self):
        self.repo = MySQLRepo() # Phụ thuộc trực tiếp vào MySQL
```

**✅ Đúng (Áp dụng Dependency Injection):**
```python
class UserService:
    def __init__(self, repo): # Inject interface/abstraction vào
        self.repo = repo
```

### 🧠 Mental Model
> "Đừng bao giờ dùng từ khóa `new` (hoặc khởi tạo trực tiếp dependency) bên trong class. Hãy để bên ngoài 'tiêm' nó vào."

---

## 🔥 Tổng hợp nhanh

| Principle | Ý nghĩa cốt lõi |
| :--- | :--- |
| **S**RP | 1 class = 1 nhiệm vụ duy nhất. |
| **O**CP | Thêm tính năng mới = Viết class mới. |
| **L**SP | Class con phải "ngoan", không được làm hỏng hành vi của cha. |
| **I**SP | Chia nhỏ interface, dùng gì implement nấy. |
| **D**IP | Phụ thuộc vào Abstraction (Interface), không phụ thuộc vào Class cụ thể. |

---

## 🔥 Mối quan hệ với Design Patterns

| SOLID Principle | Pattern liên quan thường dùng |
| :--- | :--- |
| **SRP** | Tách nhỏ Class, Facade |
| **OCP** | Strategy, Factory Method, Decorator |
| **LSP** | Inheritance chuẩn, Template Method |
| **ISP** | Interface Design |
| **DIP** | **Dependency Injection (DI)** |

---

## 🎯 Lời khuyên thực chiến
1.  **Đừng "cuồng" SOLID:** Đừng cố tách nhỏ mọi thứ khi hệ thống còn đơn giản. Nó dễ dẫn đến **Over-engineering** (làm phức tạp hóa vấn đề).
2.  **Viết code trước, Refactor sau:** Hãy viết cho chạy được, sau đó quan sát các **Code Smell** (if/else dài, class to...) rồi mới áp dụng SOLID để sửa.
3.  **Quy trình chuẩn:** * Viết code ➡️ Thấy Code Smell ➡️ Đối chiếu SOLID ➡️ Chọn Design Pattern phù hợp ➡️ Refactor.

## 🚀 Cách luyện tập
Đừng học thuộc lòng lý thuyết. Khi code, hãy tự đặt câu hỏi:
* *"Nếu mình thêm một loại Database mới, mình có phải sửa file này không?"* (Nếu có → Vi phạm **DIP/OCP**).
* *"Hàm này đang làm 3 việc khác nhau đúng không?"* (Nếu có → Vi phạm **SRP**).

---