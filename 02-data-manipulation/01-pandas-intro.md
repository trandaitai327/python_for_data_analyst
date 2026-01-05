# 01. Pandas Introduction

## Mục Tiêu
- Hiểu về Pandas và tại sao nó quan trọng cho Data Analysis
- Tạo và làm việc với Series và DataFrame
- Đọc và ghi file CSV, Excel
- Truy cập và lọc dữ liệu cơ bản

## Pandas Là Gì?

Pandas là thư viện Python mạnh mẽ cho:
- **Xử lý dữ liệu**: Làm sạch, chuyển đổi, phân tích
- **Đọc/ghi file**: CSV, Excel, JSON, SQL, và nhiều format khác
- **Phân tích**: Thống kê, grouping, pivoting
- **Tích hợp**: Làm việc tốt với NumPy, Matplotlib

## Cài Đặt

```bash
pip install pandas openpyxl
```

## 1. Series

Series là một mảng một chiều với index (nhãn).

```python
import pandas as pd

# Tạo Series từ list
sales = pd.Series([100000, 120000, 150000, 180000])
print(sales)

# Tạo Series với index tùy chỉnh
sales = pd.Series(
    [100000, 120000, 150000, 180000],
    index=["Q1", "Q2", "Q3", "Q4"]
)
print(sales)

# Truy cập giá trị
print(sales["Q1"])  # 100000
print(sales[0])  # 100000
```

**Ví dụ thực tế**: Doanh số theo quý
```python
quarterly_sales = pd.Series(
    [50000000, 55000000, 60000000, 65000000],
    index=["Q1", "Q2", "Q3", "Q4"]
)

print("Doanh số theo quý:")
print(quarterly_sales)
print(f"\nTổng: {quarterly_sales.sum():,} VND")
print(f"Trung bình: {quarterly_sales.mean():,.0f} VND")
print(f"Cao nhất: {quarterly_sales.max():,} VND")
```

## 2. DataFrame

DataFrame là bảng 2 chiều (như Excel), có rows và columns.

### Tạo DataFrame

```python
# Từ dictionary
data = {
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor"],
    "Price": [15000000, 500000, 2000000, 5000000],
    "Stock": [50, 200, 100, 80]
}

df = pd.DataFrame(data)
print(df)
```

**Ví dụ thực tế**: Dữ liệu sản phẩm
```python
# Tạo DataFrame từ dictionary
products = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"],
    "Category": ["Electronics", "Accessories", "Accessories", "Electronics", "Audio"],
    "Price": [15000000, 500000, 2000000, 5000000, 3000000],
    "Stock": [50, 200, 100, 80, 60],
    "Sales": [30, 150, 80, 50, 40]
})

print("Dữ liệu sản phẩm:")
print(products)
print(f"\nKích thước: {products.shape}")  # (5, 5) - 5 rows, 5 columns
print(f"\nThông tin:")
print(products.info())
```

### Đọc File CSV

```python
# Đọc CSV
df = pd.read_csv("sales_data.csv")

# Xem vài dòng đầu
print(df.head())  # 5 dòng đầu
print(df.head(10))  # 10 dòng đầu

# Xem vài dòng cuối
print(df.tail())  # 5 dòng cuối

# Thông tin tổng quan
print(df.info())  # Kiểu dữ liệu, số lượng non-null
print(df.describe())  # Thống kê mô tả
```

**Ví dụ thực tế**: Tạo và lưu file CSV mẫu
```python
# Tạo dữ liệu mẫu
sales_data = pd.DataFrame({
    "Date": ["2024-01-01", "2024-01-02", "2024-01-03", "2024-01-04", "2024-01-05"],
    "Product": ["Laptop", "Mouse", "Keyboard", "Laptop", "Monitor"],
    "Quantity": [5, 20, 15, 3, 8],
    "Price": [15000000, 500000, 2000000, 15000000, 5000000],
    "Total": [75000000, 10000000, 30000000, 45000000, 40000000]
})

# Lưu vào CSV
sales_data.to_csv("sales_data.csv", index=False)

# Đọc lại
df = pd.read_csv("sales_data.csv")
print(df)
```

### Đọc File Excel

```python
# Đọc Excel
df = pd.read_excel("sales_data.xlsx", sheet_name="Sheet1")

# Đọc nhiều sheets
excel_file = pd.ExcelFile("sales_data.xlsx")
df1 = excel_file.parse("Sheet1")  # hoặc pd.read_excel("sales_data.xlsx", sheet_name="Sheet1")
df2 = excel_file.parse("Sheet2")  # hoặc pd.read_excel("sales_data.xlsx", sheet_name="Sheet2")

# Ghi Excel
df.to_excel("output.xlsx", index=False)
```

## 3. Truy Cập Dữ Liệu

### Truy Cập Columns

```python
# Cách 1: Dùng tên column như attribute
print(df.Product)
print(df.Price)

# Cách 2: Dùng bracket notation (khuyến nghị)
print(df["Product"])
print(df["Price"])

# Nhiều columns
print(df[["Product", "Price", "Stock"]])
```

### Truy Cập Rows

```python
# Theo index
print(df.iloc[0])  # Dòng đầu tiên
print(df.iloc[0:3])  # 3 dòng đầu

# Theo label (nếu có index tùy chỉnh)
df_indexed = df.set_index("Product")
print(df_indexed.loc["Laptop"])  # Dòng có Product = "Laptop"
```

### Lọc Dữ Liệu

```python
# Lọc theo điều kiện
expensive = df[df["Price"] > 5000000]
print("Sản phẩm đắt:")
print(expensive)

# Nhiều điều kiện
high_value = df[(df["Price"] > 5000000) & (df["Stock"] < 100)]
print("\nSản phẩm đắt và ít tồn kho:")
print(high_value)

# Lọc theo giá trị trong list
categories = df[df["Category"].isin(["Electronics", "Audio"])]
print("\nSản phẩm Electronics và Audio:")
print(categories)
```

**Ví dụ thực tế**: Phân tích doanh số
```python
# Tạo dữ liệu bán hàng
sales = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=10, freq="D"),
    "Product": ["Laptop", "Mouse", "Keyboard", "Laptop", "Monitor", 
                "Mouse", "Keyboard", "Laptop", "Monitor", "Speaker"],
    "Quantity": [5, 20, 15, 3, 8, 25, 18, 7, 6, 10],
    "Price": [15000000, 500000, 2000000, 15000000, 5000000,
              500000, 2000000, 15000000, 5000000, 3000000]
})

# Tính tổng tiền
sales["Total"] = sales["Quantity"] * sales["Price"]

# Lọc đơn hàng lớn (> 50 triệu)
large_orders = sales[sales["Total"] > 50000000]
print("Đơn hàng lớn:")
print(large_orders)

# Lọc theo sản phẩm
laptop_sales = sales[sales["Product"] == "Laptop"]
print(f"\nDoanh số Laptop: {laptop_sales['Total'].sum():,} VND")
```

## 4. Thao Tác Cơ Bản

### Thêm/Sửa/Xóa Columns

```python
# Thêm column mới
df["Revenue"] = df["Price"] * df["Sales"]
df["InStock"] = df["Stock"] > 0

# Sửa column
df["Price"] = df["Price"] * 1.1  # Tăng giá 10%

# Đổi tên column
df = df.rename(columns={"Price": "Unit_Price"})

# Xóa column
df = df.drop(columns=["InStock"])
```

### Thêm Rows

```python
# Thêm một row
new_row = pd.DataFrame({
    "Product": ["Tablet"],
    "Category": ["Electronics"],
    "Price": [8000000],
    "Stock": [40],
    "Sales": [25]
})

df = pd.concat([df, new_row], ignore_index=True)
```

### Sắp Xếp

```python
# Sắp xếp theo column
df_sorted = df.sort_values("Price", ascending=False)
print("Sắp xếp theo giá (cao -> thấp):")
print(df_sorted)

# Sắp xếp theo nhiều columns
df_sorted = df.sort_values(["Category", "Price"], ascending=[True, False])
```

## 5. Thống Kê Cơ Bản

```python
# Thống kê mô tả
print(df.describe())

# Thống kê cho column cụ thể
print(f"Giá trung bình: {df['Price'].mean():,.0f}")
print(f"Giá cao nhất: {df['Price'].max():,}")
print(f"Giá thấp nhất: {df['Price'].min():,}")
print(f"Tổng tồn kho: {df['Stock'].sum()}")

# Đếm
print(f"Số sản phẩm: {df['Product'].count()}")
print(f"Số category: {df['Category'].nunique()}")
print(f"Phân bố category:")
print(df['Category'].value_counts())
```

**Ví dụ thực tế**: Báo cáo tổng hợp
```python
# Tạo báo cáo tổng hợp
summary = {
    "Tổng số sản phẩm": len(df),
    "Tổng giá trị tồn kho": (df["Price"] * df["Stock"]).sum(),
    "Giá trung bình": df["Price"].mean(),
    "Sản phẩm đắt nhất": df.loc[df["Price"].idxmax(), "Product"],
    "Sản phẩm rẻ nhất": df.loc[df["Price"].idxmin(), "Product"]
}

print("BÁO CÁO TỔNG HỢP")
print("-" * 40)
for key, value in summary.items():
    if isinstance(value, float):
        print(f"{key}: {value:,.0f} VND")
    else:
        print(f"{key}: {value}")
```

## Bài Tập Thực Hành

### Bài 1: Tạo và Phân Tích DataFrame
```python
# Tạo DataFrame với dữ liệu nhân viên:
# - Name, Department, Salary, Years_Experience
# Ít nhất 10 nhân viên

# Tính:
# 1. Lương trung bình theo department
# 2. Nhân viên có lương cao nhất
# 3. Lọc nhân viên có kinh nghiệm > 5 năm
```

### Bài 2: Đọc và Phân Tích File
```python
# Tạo file CSV với dữ liệu bán hàng (Date, Product, Quantity, Price)
# Đọc file và tính:
# 1. Tổng doanh số
# 2. Doanh số theo sản phẩm
# 3. Top 5 ngày có doanh số cao nhất
```

### Bài 3: Lọc và Sắp Xếp
```python
# Từ DataFrame sản phẩm:
# 1. Lọc sản phẩm có giá > 5 triệu và tồn kho < 100
# 2. Sắp xếp theo category, sau đó theo giá (giảm dần)
# 3. Tạo column mới "Status": "High" nếu Price > 5tr, "Low" nếu không
```

## Tổng Kết

✅ Đã học:
- Series và DataFrame
- Đọc/ghi CSV và Excel
- Truy cập và lọc dữ liệu
- Thao tác cơ bản với DataFrame
- Thống kê mô tả

**Tiếp theo**: [02. Data Cleaning](02-data-cleaning.md)

