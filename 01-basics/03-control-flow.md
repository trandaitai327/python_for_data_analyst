# 03. Control Flow

## Mục Tiêu
- Sử dụng if/else để đưa ra quyết định
- Sử dụng loops để lặp qua dữ liệu
- Hiểu list comprehensions
- Áp dụng vào phân tích dữ liệu thực tế

## 1. Conditional Statements (If/Else)

Cho phép code thực hiện các hành động khác nhau dựa trên điều kiện.

### Cú Pháp Cơ Bản

```python
# If đơn giản
revenue = 1000000
if revenue > 500000:
    print("Doanh số tốt!")

# If-else
target = 1000000
actual = 1200000

if actual >= target:
    print("Đạt target!")
else:
    print("Chưa đạt target")

# If-elif-else (nhiều điều kiện)
score = 85

if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
elif score >= 70:
    grade = "C"
else:
    grade = "D"

print(f"Điểm: {score}, Xếp loại: {grade}")
```

### Comparison Operators (Toán Tử So Sánh)

```python
# So sánh số
a = 10
b = 5

print(a > b)   # True (lớn hơn)
print(a < b)   # False (nhỏ hơn)
print(a >= b)  # True (lớn hơn hoặc bằng)
print(a <= b)  # False (nhỏ hơn hoặc bằng)
print(a == b)  # False (bằng)
print(a != b)  # True (khác)
```

### Logical Operators (Toán Tử Logic)

```python
# AND: cả hai điều kiện đều True
age = 25
has_license = True

if age >= 18 and has_license:
    print("Có thể lái xe")

# OR: ít nhất một điều kiện True
is_weekend = True
is_holiday = False

if is_weekend or is_holiday:
    print("Ngày nghỉ")

# NOT: đảo ngược kết quả
is_raining = False

if not is_raining:
    print("Trời không mưa, có thể đi chơi")
```

**Ví dụ thực tế**: Phân loại khách hàng
```python
# Phân loại khách hàng theo giá trị đơn hàng
order_value = 5000000

if order_value >= 10000000:
    customer_tier = "VIP"
    discount = 0.15
elif order_value >= 5000000:
    customer_tier = "Gold"
    discount = 0.10
elif order_value >= 2000000:
    customer_tier = "Silver"
    discount = 0.05
else:
    customer_tier = "Regular"
    discount = 0.0

print(f"Khách hàng: {customer_tier}")
print(f"Giảm giá: {discount:.0%}")
print(f"Giá sau giảm: {order_value * (1 - discount):,.0f} VND")
```

**Ví dụ thực tế**: Đánh giá hiệu suất
```python
# Đánh giá hiệu suất nhân viên
sales_target = 10000000
actual_sales = 12000000
customer_satisfaction = 4.5

# Tính phần trăm đạt target
performance = (actual_sales / sales_target) * 100

# Đánh giá
if performance >= 120 and customer_satisfaction >= 4.5:
    rating = "Xuất sắc"
    bonus = 0.2
elif performance >= 100 and customer_satisfaction >= 4.0:
    rating = "Tốt"
    bonus = 0.1
elif performance >= 80:
    rating = "Đạt"
    bonus = 0.05
else:
    rating = "Cần cải thiện"
    bonus = 0

print(f"Đánh giá: {rating}")
print(f"Thưởng: {bonus:.0%}")
```

## 2. Loops (Vòng Lặp)

### For Loop

Dùng để lặp qua một tập hợp (list, string, range...)

```python
# Lặp qua list
sales = [100000, 120000, 150000, 180000]

for sale in sales:
    print(f"Doanh số: {sale:,} VND")

# Lặp với index
for i, sale in enumerate(sales):
    print(f"Tháng {i+1}: {sale:,} VND")
```

**Ví dụ thực tế**: Tính tổng và trung bình
```python
# Doanh số theo tháng
monthly_sales = [50000000, 55000000, 60000000, 65000000, 70000000]

# Tính tổng
total = 0
for sale in monthly_sales:
    total += sale

print(f"Tổng doanh số: {total:,} VND")
print(f"Trung bình: {total/len(monthly_sales):,.0f} VND")

# Tìm tháng có doanh số cao nhất
max_sale = 0
best_month = 0

for i, sale in enumerate(monthly_sales):
    if sale > max_sale:
        max_sale = sale
        best_month = i + 1

print(f"Tháng tốt nhất: Tháng {best_month} với {max_sale:,} VND")
```

### Range Function

Tạo dãy số để lặp:

```python
# range(stop) - từ 0 đến stop-1
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# range(start, stop) - từ start đến stop-1
for i in range(1, 6):
    print(i)  # 1, 2, 3, 4, 5

# range(start, stop, step) - với bước nhảy
for i in range(0, 10, 2):
    print(i)  # 0, 2, 4, 6, 8
```

**Ví dụ thực tế**: Tạo báo cáo
```python
# Tạo báo cáo 12 tháng
months = ["Tháng 1", "Tháng 2", "Tháng 3", "Tháng 4", "Tháng 5", "Tháng 6",
          "Tháng 7", "Tháng 8", "Tháng 9", "Tháng 10", "Tháng 11", "Tháng 12"]
sales = [50, 55, 60, 65, 70, 75, 80, 85, 90, 95, 100, 105]  # triệu VND

print("BÁO CÁO DOANH SỐ NĂM")
print("-" * 40)
for i in range(len(months)):
    print(f"{months[i]}: {sales[i]:,} triệu VND")
```

### While Loop

Lặp khi điều kiện còn True:

```python
# Đếm ngược
countdown = 5
while countdown > 0:
    print(countdown)
    countdown -= 1
print("Bắt đầu!")

# Tính đến khi đạt target
current_sales = 0
target = 1000000
daily_sales = 100000
days = 0

while current_sales < target:
    current_sales += daily_sales
    days += 1
    print(f"Ngày {days}: {current_sales:,} VND")

print(f"Đạt target sau {days} ngày")
```

### Loop Control: break và continue

```python
# break: dừng loop ngay lập tức
sales = [100000, 120000, 0, 150000, 180000]

for sale in sales:
    if sale == 0:
        print("Gặp lỗi dữ liệu, dừng xử lý")
        break
    print(f"Doanh số: {sale:,}")

# continue: bỏ qua lần lặp hiện tại
for sale in sales:
    if sale == 0:
        print("Bỏ qua giá trị 0")
        continue
    print(f"Doanh số: {sale:,}")
```

**Ví dụ thực tế**: Lọc dữ liệu
```python
# Lọc doanh số hợp lệ (> 0)
all_sales = [100000, -50000, 0, 150000, 200000, -10000]
valid_sales = []

for sale in all_sales:
    if sale <= 0:
        continue  # Bỏ qua giá trị không hợp lệ
    valid_sales.append(sale)

print(f"Doanh số hợp lệ: {valid_sales}")
print(f"Tổng: {sum(valid_sales):,} VND")
```

## 3. List Comprehensions

Cách ngắn gọn để tạo list từ loop:

```python
# Cách thông thường
squares = []
for i in range(5):
    squares.append(i ** 2)
print(squares)  # [0, 1, 4, 9, 16]

# List comprehension
squares = [i ** 2 for i in range(5)]
print(squares)  # [0, 1, 4, 9, 16]
```

**Ví dụ thực tế**: Xử lý dữ liệu
```python
# Chuyển đổi đơn vị (triệu -> nghìn)
sales_millions = [50, 55, 60, 65, 70]
sales_thousands = [x * 1000 for x in sales_millions]
print(sales_thousands)  # [50000, 55000, 60000, 65000, 70000]

# Lọc dữ liệu
all_sales = [100000, -50000, 0, 150000, 200000]
positive_sales = [x for x in all_sales if x > 0]
print(positive_sales)  # [100000, 150000, 200000]

# Tính toán phức tạp
prices = [1000000, 1500000, 2000000]
prices_with_tax = [price * 1.1 for price in prices]  # Thêm 10% thuế
print(prices_with_tax)
```

**Ví dụ thực tế**: Phân tích doanh số
```python
# Tính tăng trưởng từng tháng
monthly_sales = [50000000, 55000000, 60000000, 65000000, 70000000]

# Tính tăng trưởng (so với tháng trước)
growth_rates = []
for i in range(1, len(monthly_sales)):
    growth = ((monthly_sales[i] - monthly_sales[i-1]) / monthly_sales[i-1]) * 100
    growth_rates.append(growth)

print("Tăng trưởng từng tháng:")
for i, rate in enumerate(growth_rates, start=2):
    print(f"Tháng {i}: {rate:.2f}%")

# Hoặc dùng list comprehension
growth_rates = [((monthly_sales[i] - monthly_sales[i-1]) / monthly_sales[i-1]) * 100 
                for i in range(1, len(monthly_sales))]
```

## 4. Nested Loops (Vòng Lặp Lồng Nhau)

```python
# Tạo bảng nhân
for i in range(1, 4):
    for j in range(1, 4):
        print(f"{i} x {j} = {i*j}", end="  ")
    print()  # Xuống dòng

# Phân tích doanh số theo sản phẩm và tháng
products = ["Laptop", "Mouse", "Keyboard"]
months = ["Tháng 1", "Tháng 2", "Tháng 3"]
sales_data = [
    [10, 12, 15],  # Laptop
    [50, 55, 60],  # Mouse
    [20, 22, 25]   # Keyboard
]

for i, product in enumerate(products):
    print(f"\n{product}:")
    for j, month in enumerate(months):
        print(f"  {month}: {sales_data[i][j]:,} sản phẩm")
```

## Bài Tập Thực Hành

### Bài 1: Phân Loại Khách Hàng
```python
# Cho danh sách giá trị đơn hàng:
order_values = [500000, 1500000, 5000000, 12000000, 2000000, 8000000]

# Phân loại:
# - VIP: >= 10,000,000
# - Gold: >= 5,000,000
# - Silver: >= 2,000,000
# - Regular: < 2,000,000

# Đếm số lượng khách hàng mỗi loại
```

### Bài 2: Tính Tăng Trưởng
```python
# Doanh số 6 tháng:
sales = [50000000, 55000000, 60000000, 65000000, 70000000, 75000000]

# Tính:
# 1. Tăng trưởng từng tháng (so với tháng trước)
# 2. Tăng trưởng trung bình
# 3. Tháng có tăng trưởng cao nhất
```

### Bài 3: Lọc và Phân Tích
```python
# Dữ liệu doanh số (có thể có giá trị âm hoặc 0):
sales_data = [100000, -50000, 0, 150000, 200000, -10000, 300000]

# 1. Lọc chỉ lấy giá trị dương
# 2. Tính tổng, trung bình
# 3. Tìm min, max
```

## Tổng Kết

✅ Đã học:
- If/else/elif cho điều kiện
- For loops và while loops
- List comprehensions
- Nested loops
- break và continue

**Tiếp theo**: [04. Functions & Modules](04-functions.md)

