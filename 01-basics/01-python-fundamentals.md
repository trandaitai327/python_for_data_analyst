# 01. Python Fundamentals

## Mục Tiêu
- Hiểu về Python và tại sao nó phù hợp cho Data Analysis
- Cài đặt Python và môi trường làm việc
- Nắm vững cú pháp cơ bản: print, comments, variables
- Làm quen với Jupyter Notebook

## Tại Sao Python Cho Data Analysis?

Python là ngôn ngữ lập trình:
- **Dễ học**: Cú pháp đơn giản, gần với ngôn ngữ tự nhiên
- **Mạnh mẽ**: Xử lý được dữ liệu lớn, phức tạp
- **Cộng đồng lớn**: Nhiều thư viện chuyên biệt (pandas, numpy, matplotlib)
- **Linh hoạt**: Từ phân tích đơn giản đến machine learning

## Cài Đặt Python

### Windows
1. Tải Python từ [python.org](https://www.python.org/downloads/)
2. Chọn "Add Python to PATH" khi cài đặt
3. Kiểm tra: mở Command Prompt, gõ `python --version`

### Mac/Linux
```bash
# Mac (với Homebrew)
brew install python3

# Linux (Ubuntu/Debian)
sudo apt-get install python3
```

## Cài Đặt Jupyter Notebook

Jupyter Notebook là công cụ tuyệt vời cho Data Analysis vì:
- Chạy code từng phần
- Kết hợp code, text, và visualizations
- Dễ chia sẻ và trình bày

```bash
pip install jupyter
jupyter notebook
```

## Cú Pháp Cơ Bản

### 1. Print Statement

```python
# In ra màn hình
print("Hello, Data Analyst!")
print("Chào mừng đến với Python")

# In nhiều giá trị
print("Doanh thu:", 1000000, "VND")
```

**Ví dụ thực tế**: In kết quả phân tích
```python
print("Tổng doanh thu tháng 1:", 50000000)
print("Tăng trưởng:", "15%")
```

### 2. Comments (Ghi Chú)

```python
# Đây là comment một dòng
# Giúp giải thích code cho người khác (và chính bạn sau này)

"""
Đây là comment nhiều dòng
Rất hữu ích cho documentation
"""

# Ví dụ: Tính doanh thu trung bình
total_revenue = 1000000  # Tổng doanh thu
num_months = 12  # Số tháng
avg_revenue = total_revenue / num_months
```

### 3. Variables (Biến)

Biến lưu trữ dữ liệu để sử dụng sau.

```python
# Đặt tên biến
company_name = "ABC Corp"
revenue_2023 = 1000000000
growth_rate = 0.15  # 15%

# Sử dụng biến
print("Công ty:", company_name)
print("Doanh thu 2023:", revenue_2023)
```

**Quy tắc đặt tên biến**:
- Bắt đầu bằng chữ cái hoặc dấu gạch dưới
- Không chứa khoảng trắng
- Phân biệt chữ hoa/thường
- Tên có ý nghĩa (descriptive)

```python
# ✅ Tốt
customer_count = 1000
monthly_revenue = 50000

# ❌ Không tốt
x = 1000
a = 50000
```

### 4. Basic Math Operations

```python
# Cộng, trừ, nhân, chia
revenue_q1 = 1000000
revenue_q2 = 1200000
revenue_q3 = 1500000
revenue_q4 = 1800000

# Tổng doanh thu năm
total_revenue = revenue_q1 + revenue_q2 + revenue_q3 + revenue_q4
print("Tổng doanh thu năm:", total_revenue)

# Trung bình
avg_revenue = total_revenue / 4
print("Doanh thu trung bình/quý:", avg_revenue)

# Tăng trưởng Q2 so với Q1
growth = ((revenue_q2 - revenue_q1) / revenue_q1) * 100
print(f"Tăng trưởng Q2: {growth:.2f}%")
```

### 5. F-strings (String Formatting)

Cách hiện đại để format strings trong Python.

```python
product = "Laptop"
price = 15000000
quantity = 5

# Cách cũ (không khuyến nghị)
print("Sản phẩm: " + product + ", Giá: " + str(price))

# Cách mới với f-string (khuyến nghị)
print(f"Sản phẩm: {product}, Giá: {price:,} VND")
print(f"Tổng tiền: {price * quantity:,} VND")

# Format số thập phân
growth_rate = 0.1523
print(f"Tăng trưởng: {growth_rate:.2%}")  # 15.23%
```

## Ví Dụ Thực Tế: Tính Toán Doanh Số

```python
# Dữ liệu bán hàng
jan_sales = 50000000
feb_sales = 55000000
mar_sales = 60000000

# Tính tổng Q1
q1_total = jan_sales + feb_sales + mar_sales
print(f"Tổng doanh số Q1: {q1_total:,} VND")

# Tính trung bình
q1_avg = q1_total / 3
print(f"Doanh số trung bình/tháng: {q1_avg:,.0f} VND")

# Tính tăng trưởng tháng 2
feb_growth = ((feb_sales - jan_sales) / jan_sales) * 100
print(f"Tăng trưởng tháng 2: {feb_growth:.1f}%")

# Tính tăng trưởng tháng 3
mar_growth = ((mar_sales - feb_sales) / feb_sales) * 100
print(f"Tăng trưởng tháng 3: {mar_growth:.1f}%")
```

## Bài Tập Thực Hành

### Bài 1: Tính Lương Nhân Viên
```python
# Cho biết:
# - Lương cơ bản: 10,000,000 VND
# - Phụ cấp: 2,000,000 VND
# - Thưởng: 15% lương cơ bản
# Tính tổng lương thực nhận
```

### Bài 2: Phân Tích Doanh Số
```python
# Cho doanh số 4 quý:
# Q1: 100 triệu, Q2: 120 triệu, Q3: 150 triệu, Q4: 180 triệu
# Tính:
# - Tổng doanh số năm
# - Doanh số trung bình/quý
# - Tăng trưởng từng quý (so với quý trước)
```

## Tổng Kết

✅ Đã học:
- Cài đặt Python và Jupyter Notebook
- Print statements và comments
- Variables và naming conventions
- Math operations
- F-strings cho formatting

**Tiếp theo**: [02. Data Types & Variables](02-data-types.md)

