# 01. Matplotlib Basics

## Mục Tiêu
- Tạo các biểu đồ cơ bản: line, bar, scatter, histogram
- Tùy chỉnh màu sắc, labels, legends
- Lưu biểu đồ
- Tạo subplots

## Matplotlib Là Gì?

Matplotlib là thư viện Python mạnh mẽ để tạo visualizations:
- **Line plots**: Xu hướng theo thời gian
- **Bar charts**: So sánh categories
- **Scatter plots**: Mối quan hệ giữa 2 biến
- **Histograms**: Phân bố dữ liệu

## Cài Đặt

```bash
pip install matplotlib
```

## 1. Line Plot (Biểu Đồ Đường)

### Cơ Bản

```python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Dữ liệu đơn giản
months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun"]
sales = [50000000, 55000000, 60000000, 65000000, 70000000, 75000000]

# Tạo line plot
plt.figure(figsize=(10, 6))
plt.plot(months, sales, marker='o', linewidth=2, markersize=8)
plt.title("Doanh Số Theo Tháng", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Nhiều Đường

```python
# Nhiều sản phẩm
months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun"]
laptop_sales = [30, 35, 40, 45, 50, 55]
mouse_sales = [150, 160, 170, 180, 190, 200]
keyboard_sales = [80, 85, 90, 95, 100, 105]

plt.figure(figsize=(10, 6))
plt.plot(months, laptop_sales, marker='o', label='Laptop', linewidth=2)
plt.plot(months, mouse_sales, marker='s', label='Mouse', linewidth=2)
plt.plot(months, keyboard_sales, marker='^', label='Keyboard', linewidth=2)
plt.title("Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: Xu hướng doanh số
```python
# Dữ liệu doanh số 12 tháng
dates = pd.date_range("2024-01-01", periods=12, freq="M")
revenue = [50000000 + i*5000000 + np.random.randint(-2000000, 2000000) 
           for i in range(12)]

plt.figure(figsize=(12, 6))
plt.plot(dates, revenue, marker='o', linewidth=2, markersize=6, color='#2E86AB')
plt.title("Xu Hướng Doanh Số 12 Tháng", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.xticks(rotation=45)
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## 2. Bar Chart (Biểu Đồ Cột)

### Bar Chart Đơn Giản

```python
# Dữ liệu doanh số theo sản phẩm
products = ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"]
sales = [450000000, 75000000, 160000000, 250000000, 120000000]

plt.figure(figsize=(10, 6))
plt.bar(products, sales, color=['#2E86AB', '#A23B72', '#F18F01', '#C73E1D', '#6A994E'])
plt.title("Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Sản Phẩm", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Horizontal Bar Chart

```python
plt.figure(figsize=(10, 6))
plt.barh(products, sales, color='#2E86AB')
plt.title("Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Doanh Số (VND)", fontsize=12)
plt.ylabel("Sản Phẩm", fontsize=12)
plt.tight_layout()
plt.show()
```

### Grouped Bar Chart

```python
# Dữ liệu nhiều tháng
products = ["Laptop", "Mouse", "Keyboard"]
jan_sales = [30, 150, 80]
feb_sales = [35, 160, 85]
mar_sales = [40, 170, 90]

x = np.arange(len(products))
width = 0.25

plt.figure(figsize=(10, 6))
plt.bar(x - width, jan_sales, width, label='Tháng 1', color='#2E86AB')
plt.bar(x, feb_sales, width, label='Tháng 2', color='#A23B72')
plt.bar(x + width, mar_sales, width, label='Tháng 3', color='#F18F01')
plt.xlabel("Sản Phẩm", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.title("So Sánh Doanh Số 3 Tháng", fontsize=16, fontweight='bold')
plt.xticks(x, products)
plt.legend()
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: So sánh doanh số
```python
# Doanh số theo category
categories = ["Electronics", "Accessories", "Audio"]
q1_sales = [500000000, 200000000, 120000000]
q2_sales = [600000000, 250000000, 150000000]
q3_sales = [700000000, 300000000, 180000000]
q4_sales = [800000000, 350000000, 200000000]

x = np.arange(len(categories))
width = 0.2

plt.figure(figsize=(12, 6))
plt.bar(x - 1.5*width, q1_sales, width, label='Q1', color='#2E86AB')
plt.bar(x - 0.5*width, q2_sales, width, label='Q2', color='#A23B72')
plt.bar(x + 0.5*width, q3_sales, width, label='Q3', color='#F18F01')
plt.bar(x + 1.5*width, q4_sales, width, label='Q4', color='#C73E1D')
plt.xlabel("Category", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.title("Doanh Số Theo Category và Quý", fontsize=16, fontweight='bold')
plt.xticks(x, categories)
plt.legend()
plt.tight_layout()
plt.show()
```

## 3. Scatter Plot (Biểu Đồ Phân Tán)

### Scatter Plot Cơ Bản

```python
# Mối quan hệ giữa giá và số lượng bán
price = [15000000, 500000, 2000000, 5000000, 3000000]
sales = [30, 150, 80, 50, 40]

plt.figure(figsize=(10, 6))
plt.scatter(price, sales, s=100, alpha=0.6, color='#2E86AB')
plt.xlabel("Giá (VND)", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.title("Mối Quan Hệ Giữa Giá và Số Lượng Bán", fontsize=16, fontweight='bold')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Scatter Plot với Categories

```python
# Dữ liệu với categories
data = pd.DataFrame({
    "Price": [15000000, 500000, 2000000, 5000000, 3000000, 8000000],
    "Sales": [30, 150, 80, 50, 40, 25],
    "Category": ["Electronics", "Accessories", "Accessories", 
                 "Electronics", "Audio", "Electronics"]
})

colors = {"Electronics": "#2E86AB", "Accessories": "#A23B72", "Audio": "#F18F01"}

plt.figure(figsize=(10, 6))
for category in data["Category"].unique():
    subset = data[data["Category"] == category]
    plt.scatter(subset["Price"], subset["Sales"], 
               s=100, alpha=0.6, label=category, color=colors[category])
plt.xlabel("Giá (VND)", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.title("Mối Quan Hệ Giữa Giá và Số Lượng Bán", fontsize=16, fontweight='bold')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## 4. Histogram (Biểu Đồ Tần Suất)

```python
# Phân bố doanh số
sales_data = np.random.normal(50000000, 10000000, 1000)

plt.figure(figsize=(10, 6))
plt.hist(sales_data, bins=30, color='#2E86AB', alpha=0.7, edgecolor='black')
plt.xlabel("Doanh Số (VND)", fontsize=12)
plt.ylabel("Tần Suất", fontsize=12)
plt.title("Phân Bố Doanh Số", fontsize=16, fontweight='bold')
plt.grid(True, alpha=0.3, axis='y')
plt.tight_layout()
plt.show()
```

## 5. Subplots (Nhiều Biểu Đồ)

```python
# Tạo 2x2 subplots
fig, axes = plt.subplots(2, 2, figsize=(15, 12))

# Subplot 1: Line plot
months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun"]
sales = [50000000, 55000000, 60000000, 65000000, 70000000, 75000000]
axes[0, 0].plot(months, sales, marker='o', color='#2E86AB')
axes[0, 0].set_title("Doanh Số Theo Tháng")
axes[0, 0].grid(True, alpha=0.3)

# Subplot 2: Bar chart
products = ["Laptop", "Mouse", "Keyboard"]
sales = [450000000, 75000000, 160000000]
axes[0, 1].bar(products, sales, color='#A23B72')
axes[0, 1].set_title("Doanh Số Theo Sản Phẩm")
axes[0, 1].tick_params(axis='x', rotation=45)

# Subplot 3: Scatter plot
price = [15000000, 500000, 2000000, 5000000, 3000000]
sales_qty = [30, 150, 80, 50, 40]
axes[1, 0].scatter(price, sales_qty, color='#F18F01', s=100, alpha=0.6)
axes[1, 0].set_title("Giá vs Số Lượng Bán")
axes[1, 0].set_xlabel("Giá (VND)")
axes[1, 0].set_ylabel("Số Lượng")

# Subplot 4: Histogram
sales_data = np.random.normal(50000000, 10000000, 1000)
axes[1, 1].hist(sales_data, bins=30, color='#C73E1D', alpha=0.7, edgecolor='black')
axes[1, 1].set_title("Phân Bố Doanh Số")
axes[1, 1].set_xlabel("Doanh Số (VND)")
axes[1, 1].set_ylabel("Tần Suất")

plt.tight_layout()
plt.show()
```

## 6. Tùy Chỉnh Nâng Cao

### Màu Sắc và Style

```python
# Sử dụng style có sẵn
# Lưu ý: style name có thể khác tùy version matplotlib
# Các style phổ biến: 'seaborn', 'seaborn-darkgrid', 'ggplot', 'classic'
plt.style.use('seaborn-darkgrid')  # hoặc 'seaborn-v0_8-darkgrid' trong matplotlib mới

# Hoặc tùy chỉnh
plt.rcParams['figure.figsize'] = (12, 6)
plt.rcParams['font.size'] = 12
plt.rcParams['axes.labelsize'] = 12
plt.rcParams['axes.titlesize'] = 16
```

### Format Số

```python
from matplotlib.ticker import FuncFormatter

def millions(x, pos):
    return f'{x/1e6:.1f}M'

formatter = FuncFormatter(millions)

plt.figure(figsize=(10, 6))
plt.bar(products, sales, color='#2E86AB')
plt.gca().yaxis.set_major_formatter(formatter)
plt.title("Doanh Số Theo Sản Phẩm")
plt.ylabel("Doanh Số (Triệu VND)")
plt.tight_layout()
plt.show()
```

## 7. Lưu Biểu Đồ

```python
# Tạo biểu đồ
plt.figure(figsize=(10, 6))
plt.plot(months, sales, marker='o', linewidth=2)
plt.title("Doanh Số Theo Tháng")
plt.xlabel("Tháng")
plt.ylabel("Doanh Số (VND)")
plt.grid(True, alpha=0.3)
plt.tight_layout()

# Lưu với chất lượng cao
plt.savefig("sales_chart.png", dpi=300, bbox_inches='tight')
plt.savefig("sales_chart.pdf", bbox_inches='tight')
plt.show()
```

## Bài Tập Thực Hành

### Bài 1: Tạo Dashboard
```python
# Tạo 4 biểu đồ trong 1 figure:
# 1. Line plot: Doanh số 12 tháng
# 2. Bar chart: Top 5 sản phẩm bán chạy
# 3. Scatter plot: Giá vs Số lượng bán
# 4. Histogram: Phân bố doanh số
```

### Bài 2: So Sánh Nhiều Năm
```python
# Tạo line plot so sánh doanh số 3 năm
# Mỗi năm một đường, có legend
# Format số trên trục Y
```

### Bài 3: Báo Cáo Trực Quan
```python
# Tạo báo cáo với:
# - Bar chart: Doanh số theo category
# - Pie chart: Tỷ lệ doanh số
# - Lưu file với chất lượng cao
```

## Tổng Kết

✅ Đã học:
- Line plots
- Bar charts (vertical, horizontal, grouped)
- Scatter plots
- Histograms
- Subplots
- Tùy chỉnh và lưu biểu đồ

**Tiếp theo**: [02. Seaborn](02-seaborn.md)

