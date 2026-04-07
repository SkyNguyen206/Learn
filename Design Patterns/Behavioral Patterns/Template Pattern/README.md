# 🦴 Behavioral Patterns: Template Method

## 1. Định nghĩa
**Template Method** là pattern cho phép định nghĩa bộ khung (skeleton) của một thuật toán trong lớp cha, nhưng cho phép các lớp con (subclasses) ghi đè (override) các bước cụ thể của thuật toán đó mà không làm thay đổi cấu trúc chung.

* **Nói đơn giản:** Định nghĩa "công thức nấu ăn" chung, các lớp con chỉ việc thay đổi "nguyên liệu" cụ thể.



## 2. Vấn đề giải quyết
**❌ Code Smell: Nhân bản logic (Code Duplication)**
```python
def process_csv():
    load_data()
    clean_csv_logic() # Khác biệt
    analyze_csv()     # Khác biệt
    save_result()

def process_json():
    load_data()
    clean_json_logic() # Khác biệt
    analyze_json()      # Khác biệt
    save_result()
```
* **Vấn đề:** Các bước `load_data` và `save_result` bị lặp lại ở mọi nơi. Nếu bạn muốn thay đổi cách lưu kết quả, bạn phải sửa ở tất cả các hàm. Code trở nên khó bảo trì và dễ sai sót khi copy-paste.

## 3. Ý tưởng cốt lõi
* **Thay vì:** Copy toàn bộ logic cho mỗi biến thể.
* **Ta sẽ:** Định nghĩa luồng thực thi (flow) duy nhất tại một nơi (lớp cha) và để các bước chi tiết cho lớp con tự quyết định.

## 4. Cấu trúc (Structure)
1.  **AbstractClass:** Chứa `template_method()` định nghĩa thứ tự các bước. Nó cũng chứa các phương thức mặc định hoặc phương thức trừu tượng (`abstractmethod`).
2.  **ConcreteClass:** Thực thi các phương thức trừu tượng để hoàn thiện các bước cụ thể trong thuật toán.

## 5. Implement trong Python

### Bước 1: Abstract Class (Định nghĩa khung)
```python
from abc import ABC, abstractmethod

class DataProcessor(ABC):
    # Template Method: Cố định luồng xử lý
    def process(self):
        self.load_data()
        self.clean_data()
        self.analyze_data()
        self.save_result()

    def load_data(self):
        print("Standard: Loading raw data...")

    @abstractmethod
    def clean_data(self):
        pass

    @abstractmethod
    def analyze_data(self):
        pass

    def save_result(self):
        print("Standard: Saving results to Database.")
```

### Bước 2: Concrete Classes (Cụ thể hóa từng bước)
```python
class CSVProcessor(DataProcessor):
    def clean_data(self):
        print("CSV: Cleaning rows and columns...")

    def analyze_data(self):
        print("CSV: Calculating statistics...")

class JSONProcessor(DataProcessor):
    def clean_data(self):
        print("JSON: Parsing nested objects...")

    def analyze_data(self):
        print("JSON: Identifying key-value patterns...")
```

## 6. Hook Method (Kỹ thuật nâng cao) ⚓
Hook là các phương thức trống trong lớp cha, lớp con **có thể** ghi đè hoặc không (không bắt buộc như `@abstractmethod`).
```python
def before_process(self):
    pass # Hook: mặc định không làm gì
```

## 7. Use case thực tế
* **Data Pipeline (ETL):** `Extract` → `Transform` (con ghi đè) → `Load`.
* **Backend Lifecycle:** Validate request → Process logic (con ghi đè) → Log/Response.
* **Trading System:** `Fetch Market Data` → `Generate Signal` (con ghi đè) → `Execute` → `Post-trade Log`.
* **Web Frameworks:** Django/FastAPI sử dụng pattern này để quản lý vòng đời của một Request (Middlewares, Dependency flow).

## 8. Khi nào nên dùng?
* ✅ Khi bạn có một thuật toán gồm nhiều bước cố định nhưng các bước đó có các biến thể khác nhau.
* ✅ Khi muốn tránh lặp code (DRY - Don't Repeat Yourself) bằng cách kéo các phần chung lên lớp cha.

## 9. So sánh: Template Method vs. Strategy

| Tiêu chí | Template Method | Strategy |
| :--- | :--- | :--- |
| **Cơ chế** | Dựa trên kế thừa (Inheritance) | Dựa trên bao hàm (Composition) |
| **Quyền kiểm soát** | Lớp cha giữ flow | Lớp Context giữ flow |
| **Thay đổi** | Quyết định tại Compile-time | Hoán đổi linh hoạt tại **Runtime** |
| **Mục đích** | Cố định cấu trúc thuật toán | Cung cấp các thuật toán thay thế |

## 10. Dấu hiệu cần dùng (Code Smells)
* ❌ Nhiều lớp hoặc hàm có cấu trúc gần như giống hệt nhau, chỉ khác vài dòng logic ở giữa.
* ❌ Bạn thấy mình đang copy-paste một quy trình xử lý và chỉ sửa lại một vài bước nhỏ.

## 11. Best Practices
* ✅ **Giữ Template Method là `final` (nếu có thể):** Không cho phép lớp con ghi đè chính hàm luồng chính (`process`).
* ✅ **Chia nhỏ các bước:** Các bước (`step`) nên làm nhiệm vụ đơn lẻ để dễ ghi đè.
* ⚠️ **Tránh lạm dụng kế thừa:** Nếu lớp con ghi đè hầu hết tất cả các bước, có lẽ bạn nên dùng **Strategy Pattern**.

## 12. Mental Model
> **Template Method không phải là "kế thừa để dùng chung code".** Nó là việc **giữ quyền kiểm soát luồng thực thi (Control Flow)** tại một nơi duy nhất để đảm bảo tính nhất quán của thuật toán.

## 13. Insight thực chiến: Combo kết hợp 🔥
Trong các hệ thống lớn, người ta thường dùng **Template Method để định nghĩa flow** và dùng **Strategy cho các step phức tạp**.

```python
class TradingBot(ABC):
    def run(self):
        data = self.fetch_data()
        # Bước này dùng Strategy Pattern để linh hoạt chọn logic
        signal = self.strategy.generate(data) 
        self.execute(signal)

# Flow (fetch -> strategy -> execute) = Template Method
# Logic generate signal = Strategy Pattern
```

## 14. Tóm tắt 1 dòng
**Template Method định nghĩa cái "khung" của quy trình và để lớp con "điền vào chỗ trống" những chi tiết cụ thể.**