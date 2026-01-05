# 04. Functions & Modules

## Mục Tiêu
- Tạo và sử dụng functions
- Hiểu về parameters và return values
- Sử dụng built-in functions
- Import và sử dụng modules
- Tổ chức code với functions

## 1. Functions (Hàm) Là Gì?

Function là một khối code có thể tái sử dụng, thực hiện một tác vụ cụ thể.

**Lợi ích**:
- Tránh lặp lại code
- Dễ bảo trì và debug
- Code dễ đọc hơn
- Có thể test riêng

## 2. Tạo Function

### Function Đơn Giản

```python
# Định nghĩa function
def greet():
    print("Xin chào, Data Analyst!")

# Gọi function
greet()  # Xin chào, Data Analyst!
```

### Function Với Parameters

```python
# Function với một parameter
def greet(name):
    print(f"Xin chào, {name}!")

greet("Nguyễn Văn A")  # Xin chào, Nguyễn Văn A!

# Function với nhiều parameters
def calculate_total(price, quantity):
    total = price * quantity
    print(f"Tổng tiền: {total:,} VND")

calculate_total(1000000, 5)  # Tổng tiền: 5,000,000 VND
```

**Ví dụ thực tế**: Tính doanh số
```python
def calculate_revenue(price, quantity, discount=0):
    """
    Tính doanh số sau giảm giá
    
    Parameters:
    price: Giá sản phẩm
    quantity: Số lượng
    discount: Tỷ lệ giảm giá (mặc định 0)
    
    Returns:
    Tổng doanh số
    """
    subtotal = price * quantity
    discount_amount = subtotal * discount
    revenue = subtotal - discount_amount
    return revenue

# Sử dụng
revenue1 = calculate_revenue(1000000, 10, 0.1)  # Giảm 10%
print(f"Doanh số: {revenue1:,} VND")

revenue2 = calculate_revenue(1000000, 10)  # Không giảm giá
print(f"Doanh số: {revenue2:,} VND")
```

### Default Parameters

```python
def calculate_price(original_price, tax_rate=0.1, discount=0):
    """
    Tính giá sau thuế và giảm giá
    """
    price_after_discount = original_price * (1 - discount)
    final_price = price_after_discount * (1 + tax_rate)
    return final_price

# Sử dụng với default values
price1 = calculate_price(1000000)  # Chỉ giá gốc
print(f"Giá cuối: {price1:,.0f} VND")

# Override default values
price2 = calculate_price(1000000, tax_rate=0.15, discount=0.1)
print(f"Giá cuối: {price2:,.0f} VND")
```

### Return Values

```python
def calculate_average(numbers):
    """Tính trung bình của một list số"""
    if len(numbers) == 0:
        return 0  # Tránh chia cho 0
    return sum(numbers) / len(numbers)

sales = [100000, 120000, 150000, 180000]
avg = calculate_average(sales)
print(f"Doanh số trung bình: {avg:,.0f} VND")
```

**Ví dụ thực tế**: Phân tích doanh số
```python
def analyze_sales(sales_list):
    """
    Phân tích doanh số: tổng, trung bình, min, max
    """
    if not sales_list:
        return None
    
    total = sum(sales_list)
    average = total / len(sales_list)
    minimum = min(sales_list)
    maximum = max(sales_list)
    
    return {
        "total": total,
        "average": average,
        "min": minimum,
        "max": maximum,
        "count": len(sales_list)
    }

# Sử dụng
monthly_sales = [50000000, 55000000, 60000000, 65000000, 70000000]
analysis = analyze_sales(monthly_sales)

print("Phân tích doanh số:")
print(f"Tổng: {analysis['total']:,} VND")
print(f"Trung bình: {analysis['average']:,.0f} VND")
print(f"Thấp nhất: {analysis['min']:,} VND")
print(f"Cao nhất: {analysis['max']:,} VND")
```

## 3. Built-in Functions (Hàm Có Sẵn)

Python có nhiều built-in functions hữu ích:

```python
# Toán học
numbers = [10, 20, 30, 40, 50]
print(sum(numbers))  # 150
print(max(numbers))  # 50
print(min(numbers))  # 10
print(len(numbers))  # 5

# Chuyển đổi kiểu
print(int("100"))  # 100
print(str(100))  # "100"
print(float("3.14"))  # 3.14

# Kiểm tra kiểu
print(type(100))  # <class 'int'>
print(isinstance(100, int))  # True

# Range
print(list(range(5)))  # [0, 1, 2, 3, 4]
print(list(range(1, 6)))  # [1, 2, 3, 4, 5]

# Sorted
numbers = [30, 10, 40, 20, 50]
print(sorted(numbers))  # [10, 20, 30, 40, 50]
print(sorted(numbers, reverse=True))  # [50, 40, 30, 20, 10]
```

## 4. Lambda Functions (Hàm Vô Danh)

Function ngắn gọn, dùng một lần:

```python
# Function thông thường
def square(x):
    return x ** 2

# Lambda function
square = lambda x: x ** 2

print(square(5))  # 25

# Thường dùng với map, filter
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x ** 2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# Filter
sales = [100000, -50000, 200000, 0, 150000]
positive_sales = list(filter(lambda x: x > 0, sales))
print(positive_sales)  # [100000, 200000, 150000]
```

**Ví dụ thực tế**: Xử lý dữ liệu
```python
# Tính giá sau thuế cho nhiều sản phẩm
prices = [1000000, 1500000, 2000000]
tax_rate = 0.1

prices_with_tax = list(map(lambda p: p * (1 + tax_rate), prices))
print(prices_with_tax)  # [1100000.0, 1650000.0, 2200000.0]

# Lọc đơn hàng lớn
orders = [500000, 1500000, 2000000, 800000, 3000000]
large_orders = list(filter(lambda o: o >= 1000000, orders))
print(large_orders)  # [1500000, 2000000, 3000000]
```

## 5. Modules (Thư Viện)

Module là file Python chứa functions, classes, variables có thể tái sử dụng.

### Import Module

```python
# Import toàn bộ module
import math

print(math.pi)  # 3.141592653589793
print(math.sqrt(16))  # 4.0

# Import với alias (tên viết tắt)
import math as m
print(m.sqrt(25))  # 5.0

# Import function cụ thể
from math import sqrt, pi
print(sqrt(36))  # 6.0
print(pi)  # 3.141592653589793

# Import tất cả (không khuyến nghị)
from math import *
```

### Các Module Hữu Ích Cho Data Analyst

#### math - Toán học
```python
import math

print(math.ceil(4.3))  # 5 (làm tròn lên)
print(math.floor(4.7))  # 4 (làm tròn xuống)
print(round(4.5))  # 5 (làm tròn) - round() là built-in function
print(math.pow(2, 3))  # 8.0 (2^3)
```

#### datetime - Ngày tháng
```python
from datetime import datetime, timedelta

# Ngày hiện tại
today = datetime.now()
print(today)  # 2024-01-15 10:30:45.123456

# Format ngày
print(today.strftime("%Y-%m-%d"))  # 2024-01-15
print(today.strftime("%d/%m/%Y"))  # 15/01/2024

# Tính toán ngày
next_week = today + timedelta(days=7)
print(next_week)

# Parse string thành datetime
date_str = "2024-01-15"
date_obj = datetime.strptime(date_str, "%Y-%m-%d")
print(date_obj)
```

**Ví dụ thực tế**: Phân tích theo thời gian
```python
from datetime import datetime, timedelta

def get_date_range(start_date, end_date):
    """Tạo danh sách các ngày trong khoảng"""
    dates = []
    current = datetime.strptime(start_date, "%Y-%m-%d")
    end = datetime.strptime(end_date, "%Y-%m-%d")
    
    while current <= end:
        dates.append(current.strftime("%Y-%m-%d"))
        current += timedelta(days=1)
    
    return dates

# Sử dụng
dates = get_date_range("2024-01-01", "2024-01-07")
print(dates)
```

#### random - Số ngẫu nhiên
```python
import random

# Số ngẫu nhiên
print(random.randint(1, 100))  # Số nguyên từ 1-100
print(random.random())  # Số thực từ 0-1

# Chọn ngẫu nhiên từ list
products = ["Laptop", "Mouse", "Keyboard"]
print(random.choice(products))

# Trộn list
numbers = [1, 2, 3, 4, 5]
random.shuffle(numbers)
print(numbers)
```

**Ví dụ thực tế**: Tạo dữ liệu mẫu
```python
import random

def generate_sample_sales(days=30, base_sales=50000):
    """Tạo dữ liệu doanh số mẫu"""
    sales = []
    for _ in range(days):
        # Doanh số dao động ±20%
        variation = random.uniform(0.8, 1.2)
        daily_sales = base_sales * variation
        sales.append(round(daily_sales, 2))
    return sales

# Tạo dữ liệu 30 ngày
sample_sales = generate_sample_sales(30, 50000)
print(f"Tổng doanh số: {sum(sample_sales):,.0f} VND")
print(f"Trung bình: {sum(sample_sales)/len(sample_sales):,.0f} VND")
```

## 6. Tạo Module Riêng

Tạo file `sales_utils.py`:

```python
# sales_utils.py

def calculate_revenue(price, quantity, discount=0):
    """Tính doanh số"""
    return price * quantity * (1 - discount)

def calculate_growth(current, previous):
    """Tính tăng trưởng phần trăm"""
    if previous == 0:
        return 0
    return ((current - previous) / previous) * 100

def analyze_sales(sales_list):
    """Phân tích doanh số"""
    return {
        "total": sum(sales_list),
        "average": sum(sales_list) / len(sales_list),
        "min": min(sales_list),
        "max": max(sales_list)
    }
```

Sử dụng module:

```python
# Import module tự tạo
import sales_utils

# Hoặc
from sales_utils import calculate_revenue, analyze_sales

# Sử dụng
revenue = calculate_revenue(1000000, 10, 0.1)
print(f"Doanh số: {revenue:,} VND")

sales = [100000, 120000, 150000]
analysis = analyze_sales(sales)
print(analysis)
```

## Bài Tập Thực Hành

### Bài 1: Tạo Function Phân Loại Khách Hàng
```python
# Tạo function classify_customer(order_value) trả về:
# - "VIP" nếu >= 10,000,000
# - "Gold" nếu >= 5,000,000
# - "Silver" nếu >= 2,000,000
# - "Regular" nếu < 2,000,000

# Test với các giá trị: 500000, 1500000, 5000000, 12000000
```

### Bài 2: Function Tính Tăng Trưởng
```python
# Tạo function calculate_growth_rates(sales_list) 
# Trả về list tăng trưởng từng tháng (so với tháng trước)

# Test với: [50000000, 55000000, 60000000, 65000000]
```

### Bài 3: Tạo Module Utilities
```python
# Tạo file data_utils.py với các functions:
# 1. clean_data(data_list) - loại bỏ giá trị <= 0
# 2. calculate_statistics(data_list) - trả về dict với total, avg, min, max
# 3. format_currency(amount) - format số thành chuỗi tiền tệ VND

# Import và sử dụng module này
```

## Tổng Kết

✅ Đã học:
- Tạo và sử dụng functions
- Parameters và return values
- Default parameters
- Lambda functions
- Import và sử dụng modules
- Tạo module riêng

**Tiếp theo**: [Phần 2: Xử Lý Dữ Liệu với Pandas](02-data-manipulation/01-pandas-intro.md)

