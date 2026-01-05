# 02. Data Types & Variables

## Mục Tiêu
- Hiểu các kiểu dữ liệu cơ bản trong Python
- Biết khi nào sử dụng từng loại
- Chuyển đổi giữa các kiểu dữ liệu
- Xử lý strings trong phân tích dữ liệu

## Các Kiểu Dữ Liệu Cơ Bản

Python có nhiều kiểu dữ liệu, nhưng Data Analyst thường dùng:

1. **Numbers** (Số): int, float
2. **Strings** (Chuỗi): str
3. **Booleans** (Logic): bool
4. **Collections** (Tập hợp): list, dict, tuple

## 1. Numbers (Số)

### Integer (int) - Số Nguyên

```python
# Số nguyên dương
customers = 1000
orders = 500
revenue = 1000000000

# Số nguyên âm
loss = -50000
temperature = -10

# Kiểm tra kiểu
print(type(customers))  # <class 'int'>
```

**Ví dụ thực tế**: Đếm số lượng
```python
total_products = 150
sold_products = 120
remaining_products = total_products - sold_products
print(f"Sản phẩm còn lại: {remaining_products}")
```

### Float (float) - Số Thực

```python
# Số thập phân
price = 99.99
growth_rate = 0.15  # 15%
average_score = 8.5

# Kết quả phép chia luôn là float
result = 10 / 3
print(result)  # 3.3333333333333335
print(type(result))  # <class 'float'>
```

**Ví dụ thực tế**: Tính toán tỷ lệ
```python
# Tính tỷ lệ chuyển đổi
visitors = 10000
purchases = 150
conversion_rate = purchases / visitors
print(f"Tỷ lệ chuyển đổi: {conversion_rate:.2%}")  # 1.50%

# Tính giá trung bình
total_revenue = 5000000
total_orders = 100
avg_order_value = total_revenue / total_orders
print(f"Giá trị đơn hàng trung bình: {avg_order_value:,.0f} VND")
```

## 2. Strings (Chuỗi)

Strings dùng để lưu text, tên, mô tả...

```python
# Tạo string
company_name = "ABC Corporation"
product_name = 'Laptop Pro'
description = """Sản phẩm chất lượng cao
Phù hợp cho doanh nghiệp"""

# Nối chuỗi
first_name = "Nguyễn"
last_name = "Văn A"
full_name = first_name + " " + last_name
print(full_name)  # Nguyễn Văn A
```

### String Methods (Phương Thức String)

Rất hữu ích khi làm sạch dữ liệu:

```python
# Chuyển đổi chữ hoa/thường
text = "Data Analyst"
print(text.upper())  # DATA ANALYST
print(text.lower())  # data analyst
print(text.title())  # Data Analyst

# Loại bỏ khoảng trắng
dirty_text = "  Data Analyst  "
print(dirty_text.strip())  # "Data Analyst"
print(dirty_text.lstrip())  # "Data Analyst  "
print(dirty_text.rstrip())  # "  Data Analyst"

# Tìm và thay thế
text = "Python for Data Analysis"
print(text.replace("Python", "Pandas"))  # Pandas for Data Analysis

# Tách chuỗi
csv_line = "Nguyễn Văn A,25,Developer"
parts = csv_line.split(",")
print(parts)  # ['Nguyễn Văn A', '25', 'Developer']
```

**Ví dụ thực tế**: Làm sạch dữ liệu khách hàng
```python
# Dữ liệu từ form (có thể có lỗi)
customer_name = "  NGUYỄN VĂN A  "
email = "  JOHN@EMAIL.COM  "

# Làm sạch
clean_name = customer_name.strip().title()
clean_email = email.strip().lower()

print(f"Tên: {clean_name}")  # Tên: Nguyễn Văn A
print(f"Email: {clean_email}")  # Email: john@email.com
```

### String Indexing & Slicing

```python
text = "Data Analysis"

# Lấy ký tự đầu tiên
print(text[0])  # D

# Lấy ký tự cuối
print(text[-1])  # s

# Lấy một phần (slice)
print(text[0:4])  # Data
print(text[5:])  # Analysis
print(text[:4])  # Data
```

## 3. Booleans (Logic)

Chỉ có 2 giá trị: `True` hoặc `False`

```python
# So sánh
revenue_2023 = 1000000
revenue_2022 = 800000

is_growing = revenue_2023 > revenue_2022
print(is_growing)  # True

# So sánh khác
print(10 > 5)  # True
print(10 == 5)  # False
print(10 != 5)  # True
print(10 >= 10)  # True
```

**Ví dụ thực tế**: Kiểm tra điều kiện
```python
# Kiểm tra đạt target
target = 1000000
actual = 1200000

met_target = actual >= target
print(f"Đạt target: {met_target}")  # True

# Kiểm tra nhiều điều kiện
is_high_value = actual > target * 1.2
is_on_time = True
is_approved = is_high_value and is_on_time
print(f"Được phê duyệt: {is_approved}")
```

## 4. Lists (Danh Sách)

List lưu nhiều giá trị theo thứ tự.

```python
# Tạo list
sales = [100000, 120000, 150000, 180000]
products = ["Laptop", "Mouse", "Keyboard"]
mixed = [100, "text", True, 3.14]

# Truy cập phần tử
print(sales[0])  # 100000 (phần tử đầu tiên)
print(sales[-1])  # 180000 (phần tử cuối)

# Thêm phần tử
sales.append(200000)
print(sales)  # [100000, 120000, 150000, 180000, 200000]

# Độ dài list
print(len(sales))  # 5
```

**Ví dụ thực tế**: Phân tích doanh số
```python
# Doanh số theo tháng
monthly_sales = [50000000, 55000000, 60000000, 65000000, 70000000]

# Tính tổng
total_sales = sum(monthly_sales)
print(f"Tổng doanh số: {total_sales:,} VND")

# Tính trung bình
avg_sales = total_sales / len(monthly_sales)
print(f"Doanh số trung bình: {avg_sales:,.0f} VND")

# Tìm min/max
print(f"Tháng thấp nhất: {min(monthly_sales):,} VND")
print(f"Tháng cao nhất: {max(monthly_sales):,} VND")
```

## 5. Dictionaries (Từ Điển)

Dictionary lưu dữ liệu dạng key-value (khóa-giá trị).

```python
# Tạo dictionary
customer = {
    "name": "Nguyễn Văn A",
    "age": 30,
    "city": "Hà Nội",
    "purchases": 5
}

# Truy cập giá trị
print(customer["name"])  # Nguyễn Văn A
print(customer.get("age"))  # 30 (cách an toàn hơn)

# Thêm/sửa giá trị
customer["email"] = "nguyenvana@email.com"
customer["age"] = 31

# Tất cả keys và values
print(customer.keys())  # dict_keys(['name', 'age', 'city', ...])
print(customer.values())  # dict_values(['Nguyễn Văn A', 31, ...])
```

**Ví dụ thực tế**: Quản lý dữ liệu sản phẩm
```python
# Dữ liệu sản phẩm
products = {
    "Laptop": {"price": 15000000, "stock": 50, "category": "Electronics"},
    "Mouse": {"price": 500000, "stock": 200, "category": "Accessories"},
    "Keyboard": {"price": 2000000, "stock": 100, "category": "Accessories"}
}

# Truy cập thông tin
laptop_price = products["Laptop"]["price"]
print(f"Giá laptop: {laptop_price:,} VND")

# Tính tổng giá trị tồn kho
total_value = 0
for product_name, info in products.items():
    total_value += info["price"] * info["stock"]
print(f"Tổng giá trị tồn kho: {total_value:,} VND")
```

## 6. Type Conversion (Chuyển Đổi Kiểu)

Quan trọng khi làm việc với dữ liệu từ nhiều nguồn:

```python
# String sang số
price_str = "15000000"
price_int = int(price_str)
print(price_int, type(price_int))  # 15000000 <class 'int'>

# Số sang string
revenue = 1000000
revenue_str = str(revenue)
print(revenue_str, type(revenue_str))  # 1000000 <class 'str'>

# Float sang int (làm tròn xuống)
price = 99.99
price_int = int(price)
print(price_int)  # 99

# String sang float
rate_str = "0.15"
rate = float(rate_str)
print(rate)  # 0.15
```

**Ví dụ thực tế**: Xử lý dữ liệu từ CSV
```python
# Dữ liệu từ CSV (thường là string)
csv_data = ["1000000", "1200000", "1500000", "1800000"]

# Chuyển sang số để tính toán
sales_numbers = [int(x) for x in csv_data]
total = sum(sales_numbers)
print(f"Tổng: {total:,} VND")

# Hoặc với float
prices_str = ["99.99", "149.50", "199.00"]
prices = [float(x) for x in prices_str]
avg_price = sum(prices) / len(prices)
print(f"Giá trung bình: {avg_price:.2f}")
```

## Bài Tập Thực Hành

### Bài 1: Xử Lý Dữ Liệu Khách Hàng
```python
# Cho dữ liệu:
customer_data = {
    "name": "  NGUYỄN VĂN A  ",
    "age": "30",
    "purchases": ["1000000", "1500000", "2000000"]
}

# Yêu cầu:
# 1. Làm sạch tên (title case, trim)
# 2. Chuyển age sang int
# 3. Tính tổng purchases (chuyển sang số)
```

### Bài 2: Phân Tích Doanh Số
```python
# Cho doanh số các sản phẩm (dạng string):
sales_data = {
    "Laptop": "50000000",
    "Mouse": "5000000",
    "Keyboard": "10000000"
}

# Tính:
# 1. Tổng doanh số
# 2. Doanh số trung bình
# 3. Sản phẩm có doanh số cao nhất
```

## Tổng Kết

✅ Đã học:
- Numbers (int, float)
- Strings và string methods
- Booleans
- Lists và Dictionaries
- Type conversion

**Tiếp theo**: [03. Control Flow](03-control-flow.md)

