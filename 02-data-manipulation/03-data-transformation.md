# 03. Data Transformation

## Mục Tiêu
- Filtering và selecting data
- Grouping và aggregation
- Pivoting và reshaping
- Apply functions
- String operations

## 1. Filtering (Lọc Dữ Liệu)

### Điều Kiện Đơn Giản

```python
import pandas as pd
import numpy as np

# Tạo dữ liệu mẫu
sales = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"],
    "Category": ["Electronics", "Accessories", "Accessories", "Electronics", "Audio"],
    "Price": [15000000, 500000, 2000000, 5000000, 3000000],
    "Sales": [30, 150, 80, 50, 40],
    "Revenue": [450000000, 75000000, 160000000, 250000000, 120000000]
})

# Lọc theo điều kiện
expensive = sales[sales["Price"] > 5000000]
print("Sản phẩm đắt (> 5 triệu):")
print(expensive)

# Nhiều điều kiện
high_value = sales[(sales["Price"] > 2000000) & (sales["Sales"] > 50)]
print("\nSản phẩm đắt và bán chạy:")
print(high_value)

# Điều kiện OR
electronics_or_audio = sales[(sales["Category"] == "Electronics") | 
                             (sales["Category"] == "Audio")]
print("\nElectronics hoặc Audio:")
print(electronics_or_audio)
```

### Query Method

```python
# Sử dụng query (dễ đọc hơn)
result = sales.query("Price > 5000000")
result = sales.query("Price > 2000000 and Sales > 50")
result = sales.query("Category in ['Electronics', 'Audio']")
```

**Ví dụ thực tế**: Phân tích doanh số
```python
# Dữ liệu bán hàng theo ngày
daily_sales = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=30, freq="D"),
    "Product": np.random.choice(["Laptop", "Mouse", "Keyboard"], 30),
    "Quantity": np.random.randint(1, 20, 30),
    "Price": np.random.choice([15000000, 500000, 2000000], 30)
})

daily_sales["Revenue"] = daily_sales["Quantity"] * daily_sales["Price"]

# Lọc tháng 1/2024
jan_sales = daily_sales[daily_sales["Date"].dt.month == 1]

# Lọc đơn hàng lớn (> 50 triệu)
large_orders = daily_sales[daily_sales["Revenue"] > 50000000]

# Lọc sản phẩm cụ thể
laptop_sales = daily_sales[daily_sales["Product"] == "Laptop"]

print(f"Tổng doanh số tháng 1: {jan_sales['Revenue'].sum():,} VND")
print(f"Số đơn hàng lớn: {len(large_orders)}")
print(f"Doanh số Laptop: {laptop_sales['Revenue'].sum():,} VND")
```

## 2. Grouping và Aggregation

### GroupBy Cơ Bản

```python
# Group theo category
category_summary = sales.groupby("Category").agg({
    "Price": "mean",
    "Sales": "sum",
    "Revenue": "sum"
})

print("Tổng hợp theo Category:")
print(category_summary)

# Nhiều aggregations
category_stats = sales.groupby("Category").agg({
    "Price": ["mean", "min", "max"],
    "Sales": ["sum", "mean"],
    "Revenue": "sum"
})

print("\nThống kê chi tiết:")
print(category_stats)
```

### Các Hàm Aggregation Phổ Biến

```python
# Các hàm có sẵn
summary = sales.groupby("Category").agg({
    "Price": ["mean", "median", "std"],
    "Sales": ["sum", "count", "mean"],
    "Revenue": ["sum", "min", "max"]
})

# Custom aggregation
def price_range(series):
    return series.max() - series.min()

custom_summary = sales.groupby("Category").agg({
    "Price": ["mean", price_range],
    "Revenue": "sum"
})
```

**Ví dụ thực tế**: Phân tích doanh số theo thời gian
```python
# Dữ liệu bán hàng theo ngày
sales_data = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=90, freq="D"),
    "Product": np.random.choice(["Laptop", "Mouse", "Keyboard"], 90),
    "Revenue": np.random.randint(10000000, 100000000, 90)
})

# Group theo tháng
sales_data["Month"] = sales_data["Date"].dt.to_period("M")
monthly_summary = sales_data.groupby("Month").agg({
    "Revenue": ["sum", "mean", "count"]
})

print("Doanh số theo tháng:")
print(monthly_summary)

# Group theo sản phẩm và tháng
product_monthly = sales_data.groupby(["Product", "Month"])["Revenue"].sum().unstack()
print("\nDoanh số theo sản phẩm và tháng:")
print(product_monthly)
```

## 3. Pivoting và Reshaping

### Pivot Table

```python
# Tạo pivot table
pivot = sales.pivot_table(
    values="Revenue",
    index="Category",
    columns="Product",
    aggfunc="sum",
    fill_value=0
)

print("Pivot Table:")
print(pivot)

# Pivot phức tạp hơn
sales_extended = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=12, freq="M"),
    "Product": ["Laptop"] * 6 + ["Mouse"] * 6,
    "Region": ["North", "South"] * 6,
    "Revenue": np.random.randint(10000000, 50000000, 12)
})

pivot_complex = sales_extended.pivot_table(
    values="Revenue",
    index="Product",
    columns="Region",
    aggfunc="sum"
)

print("\nPivot Table phức tạp:")
print(pivot_complex)
```

### Melt (Unpivot)

```python
# Chuyển từ wide format sang long format
wide_data = pd.DataFrame({
    "Product": ["Laptop", "Mouse"],
    "Q1": [100000000, 20000000],
    "Q2": [120000000, 25000000],
    "Q3": [150000000, 30000000],
    "Q4": [180000000, 35000000]
})

long_data = wide_data.melt(
    id_vars="Product",
    value_vars=["Q1", "Q2", "Q3", "Q4"],
    var_name="Quarter",
    value_name="Revenue"
)

print("Long format:")
print(long_data)
```

**Ví dụ thực tế**: Báo cáo doanh số
```python
# Dữ liệu bán hàng
sales_report = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=12, freq="M"),
    "Laptop": np.random.randint(30000000, 50000000, 12),
    "Mouse": np.random.randint(5000000, 10000000, 12),
    "Keyboard": np.random.randint(10000000, 20000000, 12)
})

# Tạo pivot để so sánh
sales_report["Month"] = sales_report["Date"].dt.strftime("%Y-%m")
pivot_report = sales_report.pivot_table(
    values=["Laptop", "Mouse", "Keyboard"],
    index="Month",
    aggfunc="sum"
)

print("Báo cáo doanh số theo tháng:")
print(pivot_report)
```

## 4. Apply Functions

### Apply trên Series

```python
# Áp dụng function cho mỗi giá trị
def categorize_price(price):
    if price >= 10000000:
        return "High"
    elif price >= 2000000:
        return "Medium"
    else:
        return "Low"

sales["Price_Category"] = sales["Price"].apply(categorize_price)
print(sales[["Product", "Price", "Price_Category"]])

# Lambda function
sales["Price_Millions"] = sales["Price"].apply(lambda x: x / 1000000)
```

### Apply trên DataFrame

```python
# Apply function cho mỗi row
def calculate_profit(row):
    # Giả sử lợi nhuận 20%
    return row["Revenue"] * 0.2

sales["Profit"] = sales.apply(calculate_profit, axis=1)

# Apply function cho mỗi column
column_sums = sales.select_dtypes(include=[np.number]).apply(sum, axis=0)
```

**Ví dụ thực tế**: Tính toán phức tạp
```python
# Tính điểm đánh giá sản phẩm
def product_score(row):
    # Điểm dựa trên doanh số và giá
    sales_score = min(row["Sales"] / 100, 1.0) * 50  # Tối đa 50 điểm
    price_score = min(row["Price"] / 20000000, 1.0) * 50  # Tối đa 50 điểm
    return sales_score + price_score

sales["Score"] = sales.apply(product_score, axis=1)
sales_sorted = sales.sort_values("Score", ascending=False)

print("Sản phẩm xếp hạng theo điểm:")
print(sales_sorted[["Product", "Score"]])
```

## 5. String Operations

Pandas có nhiều string methods mạnh mẽ:

```python
# Tạo dữ liệu text
products = pd.DataFrame({
    "Product": ["Laptop Pro 15", "Mouse Wireless", "Keyboard Mechanical"],
    "Description": ["High performance laptop", "Ergonomic mouse", "RGB keyboard"]
})

# String operations
products["Product_Upper"] = products["Product"].str.upper()
products["Product_Lower"] = products["Product"].str.lower()
products["Word_Count"] = products["Description"].str.split().str.len()

# Extract patterns
products["Has_Pro"] = products["Product"].str.contains("Pro", case=False)
products["First_Word"] = products["Product"].str.split().str[0]

# Replace
products["Product_Clean"] = products["Product"].str.replace(" ", "_")

print(products)
```

**Ví dụ thực tế**: Làm sạch tên khách hàng
```python
# Dữ liệu khách hàng không nhất quán
customers = pd.DataFrame({
    "Name": ["  NGUYỄN VĂN A  ", "trần thị b", "Lê Văn C", "phạm thị d"],
    "Email": ["A@EMAIL.COM", "b@email.com", "C@Email.Com", "d@EMAIL.com"]
})

# Chuẩn hóa
customers["Name_Clean"] = customers["Name"].str.strip().str.title()
customers["Email_Clean"] = customers["Email"].str.strip().str.lower()

# Extract domain
customers["Domain"] = customers["Email_Clean"].str.split("@").str[1]

print("Sau khi chuẩn hóa:")
print(customers[["Name_Clean", "Email_Clean", "Domain"]])
```

## 6. Window Functions

### Rolling (Trượt)

```python
# Tạo dữ liệu theo thời gian
time_series = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=30, freq="D"),
    "Sales": np.random.randint(10000000, 50000000, 30)
})

# Rolling average (trung bình trượt)
time_series["Sales_MA7"] = time_series["Sales"].rolling(window=7).mean()
time_series["Sales_MA30"] = time_series["Sales"].rolling(window=30).mean()

# Rolling sum
time_series["Sales_Sum7"] = time_series["Sales"].rolling(window=7).sum()

print(time_series[["Date", "Sales", "Sales_MA7", "Sales_MA30"]].head(10))
```

### Expanding (Mở rộng)

```python
# Cumulative sum
time_series["Cumulative_Sales"] = time_series["Sales"].expanding().sum()

# Cumulative average
time_series["Cumulative_Avg"] = time_series["Sales"].expanding().mean()

print(time_series[["Date", "Sales", "Cumulative_Sales"]].head(10))
```

**Ví dụ thực tế**: Phân tích xu hướng
```python
# Tính tăng trưởng so với tháng trước
time_series["Sales_Prev"] = time_series["Sales"].shift(1)
time_series["Growth"] = ((time_series["Sales"] - time_series["Sales_Prev"]) / 
                         time_series["Sales_Prev"]) * 100

# Tính tăng trưởng so với cùng kỳ năm trước (nếu có)
time_series["Sales_YoY"] = time_series["Sales"].shift(365)  # Nếu có đủ dữ liệu

print("Phân tích tăng trưởng:")
print(time_series[["Date", "Sales", "Growth"]].head(10))
```

## 7. Transform và Filter

### Transform

```python
# Thêm column mới dựa trên group
sales["Category_Avg_Price"] = sales.groupby("Category")["Price"].transform("mean")
sales["Price_vs_Category_Avg"] = sales["Price"] - sales["Category_Avg_Price"]

print(sales[["Product", "Category", "Price", "Category_Avg_Price", "Price_vs_Category_Avg"]])
```

### Filter

```python
# Lọc groups
def filter_group(group):
    return group["Revenue"].sum() > 200000000

filtered = sales.groupby("Category").filter(filter_group)
print("Categories có tổng doanh số > 200 triệu:")
print(filtered)
```

## Bài Tập Thực Hành

### Bài 1: Phân Tích Doanh Số
```python
# Tạo dữ liệu bán hàng 12 tháng với nhiều sản phẩm
# Tính:
# 1. Doanh số theo tháng
# 2. Doanh số theo sản phẩm
# 3. Top 3 sản phẩm bán chạy nhất
# 4. Tăng trưởng từng tháng
```

### Bài 2: Pivot Table
```python
# Tạo pivot table:
# - Rows: Sản phẩm
# - Columns: Tháng
# - Values: Doanh số
# - Aggregation: Sum và Mean
```

### Bài 3: Window Functions
```python
# Tính:
# 1. Moving average 7 ngày
# 2. Cumulative sum
# 3. Tăng trưởng so với ngày trước
# 4. Tăng trưởng so với tuần trước
```

## Tổng Kết

✅ Đã học:
- Filtering và selecting
- Grouping và aggregation
- Pivoting và reshaping
- Apply functions
- String operations
- Window functions

**Tiếp theo**: [04. Merging & Joining](04-merging-joining.md)

