# 🏗️ Clean Architecture & Layering

## 1. Định nghĩa ngắn gọn
**Clean Architecture** là cách tổ chức hệ thống thành nhiều tầng (layers) với trách nhiệm riêng biệt. Quy tắc cốt lõi là sự phụ thuộc chỉ được hướng vào bên trong (**Inward Dependency**).



## 2. Core Idea (Quy tắc bất biến)
* **Outer Layer** (Vỏ ngoài) được phép phụ thuộc vào **Inner Layer** (Lõi trong).
* **Inner Layer** (Lõi trong) tuyệt đối **KHÔNG** được biết gì về Outer Layer.
* *Hệ quả:* Business Logic (Lõi) không được phụ thuộc vào Database hay Web Framework (Vỏ).

---

## 3. Các Layer chính (Từ trong ra ngoài)

1.  **Domain / Entity Layer:** Chứa Logic nghiệp vụ cốt lõi nhất.
2.  **Service / Use Case Layer:** Điều phối logic, thực hiện các kịch bản người dùng.
3.  **Repository / Interface Layer:** Định nghĩa cách giao tiếp với dữ liệu (Abstraction).
4.  **Controller / API / Infra Layer:** Tiếp nhận Request, implement DB cụ thể, Framework.

---

## 4. Breakdown chi tiết từng Layer

### 🔹 4.1 Domain Layer (The Core)
* **Vai trò:** Chứa các thực thể (Entities) và quy tắc nghiệp vụ không bao giờ thay đổi dù bạn đổi DB hay đổi Web framework.
* **Ví dụ:**
```python
class User:
    def __init__(self, name):
        self.name = name

    def is_valid(self):
        return len(self.name) > 0
```
* **🧠 Insight:** Domain phải "sạch" tuyệt đối. Không import `FastAPI`, không import `SQLAlchemy`.

### 🔹 4.2 Use Case / Service Layer
* **Vai trò:** Điều phối (Orchestrate) các thực thể để thực hiện một công việc cụ thể. Đây là nơi áp dụng **Facade Pattern**.
* **Ví dụ:**
```python
class CreateUserService:
    def __init__(self, repo):
        self.repo = repo # Dependency Injection

    def execute(self, data):
        user = User(data["name"])
        if not user.is_valid():
            raise Exception("Invalid User Data")
        self.repo.save(user)
        return user
```

### 🔹 4.3 Repository Layer (The Interface)
* **Vai trò:** Định nghĩa bản thiết kế (Interface) cho việc lưu trữ dữ liệu. Áp dụng **DIP (Dependency Inversion)**.
```python
class UserRepository: # Abstract Base Class
    def save(self, user):
        pass
```

### 🔹 4.4 Infrastructure Layer (Implementation)
* **Vai trò:** Cài đặt chi tiết (Details). Đây là nơi chứa code SQL, gọi API bên thứ ba.
* **Ví dụ:**
```python
class MySQLUserRepository(UserRepository): # Hiện thực hóa Interface
    def save(self, user):
        # Thực hiện query SQL tại đây
        print("Saving to MySQL...")
```
* **🧠 Insight:** Bạn có thể đổi sang `MongoUserRepository` mà không cần sửa một dòng code nào trong lớp **Service**.

### 🔹 4.5 Controller / API Layer
* **Vai trò:** Nhận input từ user (HTTP, CLI), validate cơ bản và đẩy vào Service.
* **🧠 Insight:** Controller phải **"cực mỏng" (Skinny Controller)**. Không chứa logic nghiệp vụ.

---

## 🔥 5. Dependency Flow (Luồng phụ thuộc)

`Controller` ➡️ `Service` ➡️ `Repository Interface` ⬅️ `Infrastructure (Impl)`

* **Chìa khóa:** Service phụ thuộc vào Interface (Lõi), Infrastructure cũng phụ thuộc vào Interface (Lõi) để biết mình cần implement cái gì.

---

## 🔥 6. Mapping: Clean Architecture & SOLID & Patterns

| Layer | SOLID Principle | Design Pattern |
| :--- | :--- | :--- |
| **Domain** | **SRP** (Chỉ chứa Core Logic) | **Entity** |
| **Service** | **OCP** (Mở rộng Use Case mới) | **Facade / Strategy** |
| **Repository** | **DIP** (Phụ thuộc Interface) | **Adapter** |
| **Controller** | **SRP** (Chỉ nhận/trả request) | **Decorator** (Middlewares) |

---

## 🔥 7. Ví dụ Full Flow (Trading System)

Khi bạn thực hiện một lệnh Trade:
1.  **API Layer:** Nhận request `POST /trade`.
2.  **TradeService (Facade):** Điều phối luồng (Check số dư ➡️ Check Risk).
3.  **Strategy Pattern:** Quyết định chọn thuật toán Trade nào (Scalping/Trend).
4.  **ExchangeAdapter (Adapter):** Chuyển lệnh sang format của Binance hoặc Bybit.
5.  **Infrastructure:** Gửi request thực tế đến sàn qua Internet.

---

## 🔥 8. Khi nào nên dùng & Khi nào không?

* ✅ **Nên dùng khi:** Project có xu hướng phình to, cần duy trì lâu dài (> 6 tháng), cần viết Unit Test nghiêm túc.
* ❌ **Không nên dùng khi:** Làm Script nhỏ, Hackathon (cần tốc độ), các ứng dụng CRUD cực đơn giản (1-2 bảng).

---

## 🔥 9. Code Smell nhận diện (Checklist)

* ❌ **Controller có logic:** Nếu bạn thấy tính toán `price * 0.8` trong Controller ➡️ **Sai**.
* ❌ **Service phụ thuộc DB:** Nếu Service có `import sqlalchemy` ➡️ **Sai** (Vi phạm DIP).
* ❌ **Domain import Framework:** Nếu Domain có `@app.get("/")` ➡️ **Sai**.

---

## 🔥 10. Mental Model
> **Clean Architecture không phải là chia folder cho đẹp.** Nó là việc **kiểm soát sự phụ thuộc** và **tách biệt trách nhiệm**. Nó giúp Business Logic của bạn "trường tồn" bất chấp sự thay đổi của công nghệ (Framework, Database, Library).

---

## 🎯 Tóm tắt 1 dòng
**Clean Architecture giúp bạn viết code mà "Lõi" (Nghiệp vụ) không bị dính chặt vào "Vỏ" (Framework/DB), giúp hệ thống dễ test và dễ thay đổi.**