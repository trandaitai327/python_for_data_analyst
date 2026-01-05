# 04. Merging & Joining

## Mục Tiêu
- Kết hợp nhiều DataFrames
- Hiểu các loại joins (inner, outer, left, right)
- Merge với nhiều keys
- Concatenate DataFrames
- Xử lý conflicts khi merge

## Tại Sao Cần Merge/Join?

Trong thực tế, dữ liệu thường nằm ở nhiều nguồn:
- Bảng khách hàng
- Bảng đơn hàng
- Bảng sản phẩm
- Bảng thanh toán

Cần kết hợp để phân tích toàn diện.

## 1. Merge Cơ Bản

### Inner Join

Chỉ giữ các rows có key ở cả hai bảng.

```python
import pandas as pd

# Bảng khách hàng
customers = pd.DataFrame({
    "CustomerID": [1, 2, 3, 4, 5],
    "Name": ["Nguyễn Văn A", "Trần Thị B", "Lê Văn C", "Phạm Thị D", "Hoàng Văn E"],
    "City": ["Hà Nội", "TP.HCM", "Đà Nẵng", "Hà Nội", "TP.HCM"]
})

# Bảng đơn hàng
orders = pd.DataFrame({
    "OrderID": [101, 102, 103, 104, 105],
    "CustomerID": [1, 2, 3, 1, 6],  # CustomerID 6 không có trong customers
    "Amount": [1000000, 2000000, 1500000, 3000000, 5000000],
    "Date": ["2024-01-01", "2024-01-02", "2024-01-03", "2024-01-04", "2024-01-05"]
})

# Inner join
result = pd.merge(customers, orders, on="CustomerID", how="inner")
print("Inner Join:")
print(result)
# Chỉ có CustomerID 1, 2, 3 (có ở cả hai bảng)
```

### Left Join

Giữ tất cả rows từ bảng trái, thêm dữ liệu từ bảng phải nếu có.

```python
# Left join
result = pd.merge(customers, orders, on="CustomerID", how="left")
print("\nLeft Join:")
print(result)
# Có tất cả khách hàng, đơn hàng sẽ là NaN nếu không có
```

### Right Join

Giữ tất cả rows từ bảng phải, thêm dữ liệu từ bảng trái nếu có.

```python
# Right join
result = pd.merge(customers, orders, on="CustomerID", how="right")
print("\nRight Join:")
print(result)
# Có tất cả đơn hàng, thông tin khách hàng sẽ là NaN nếu không có
```

### Outer Join (Full Join)

Giữ tất cả rows từ cả hai bảng.

```python
# Outer join
result = pd.merge(customers, orders, on="CustomerID", how="outer")
print("\nOuter Join:")
print(result)
# Có tất cả khách hàng và đơn hàng
```

**Ví dụ thực tế**: Phân tích đơn hàng
```python
# Merge để có thông tin đầy đủ
customer_orders = pd.merge(customers, orders, on="CustomerID", how="left")

# Tính tổng đơn hàng mỗi khách hàng
customer_summary = customer_orders.groupby(["CustomerID", "Name", "City"]).agg({
    "OrderID": "count",
    "Amount": "sum"
}).reset_index()

customer_summary.columns = ["CustomerID", "Name", "City", "Order_Count", "Total_Amount"]

print("Tổng hợp đơn hàng theo khách hàng:")
print(customer_summary)
```

## 2. Merge Với Nhiều Keys

```python
# Bảng sản phẩm
products = pd.DataFrame({
    "ProductID": [1, 2, 3, 4],
    "ProductName": ["Laptop", "Mouse", "Keyboard", "Monitor"],
    "Category": ["Electronics", "Accessories", "Accessories", "Electronics"],
    "Price": [15000000, 500000, 2000000, 5000000]
})

# Bảng đơn hàng chi tiết
order_details = pd.DataFrame({
    "OrderID": [101, 101, 102, 103, 104],
    "ProductID": [1, 2, 1, 3, 4],
    "Quantity": [1, 2, 1, 1, 2],
    "Discount": [0.1, 0.05, 0, 0.15, 0.1]
})

# Merge với nhiều keys
result = pd.merge(order_details, products, on="ProductID", how="left")

# Tính tổng tiền
result["Subtotal"] = result["Price"] * result["Quantity"]
result["Discount_Amount"] = result["Subtotal"] * result["Discount"]
result["Total"] = result["Subtotal"] - result["Discount_Amount"]

print("Chi tiết đơn hàng:")
print(result[["OrderID", "ProductName", "Quantity", "Price", "Total"]])
```

## 3. Merge Với Keys Khác Tên

```python
# Bảng có tên key khác nhau
customers = pd.DataFrame({
    "CustomerID": [1, 2, 3],
    "Name": ["A", "B", "C"]
})

orders = pd.DataFrame({
    "OrderID": [101, 102, 103],
    "CustID": [1, 2, 3],  # Tên khác CustomerID
    "Amount": [1000000, 2000000, 1500000]
})

# Merge với left_on và right_on
result = pd.merge(customers, orders, 
                  left_on="CustomerID", 
                  right_on="CustID", 
                  how="inner")

print("Merge với keys khác tên:")
print(result)
```

## 4. Concatenate (Nối) DataFrames

### Concatenate Theo Chiều Dọc (Rows)

```python
# Nối nhiều DataFrames có cùng cấu trúc
df1 = pd.DataFrame({
    "Product": ["Laptop", "Mouse"],
    "Sales": [30, 150]
})

df2 = pd.DataFrame({
    "Product": ["Keyboard", "Monitor"],
    "Sales": [80, 50]
})

df3 = pd.DataFrame({
    "Product": ["Speaker"],
    "Sales": [40]
})

# Concatenate
combined = pd.concat([df1, df2, df3], ignore_index=True)
print("Sau khi nối:")
print(combined)
```

### Concatenate Theo Chiều Ngang (Columns)

```python
# Nối theo columns
df1 = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard"]
})

df2 = pd.DataFrame({
    "Price": [15000000, 500000, 2000000],
    "Stock": [50, 200, 100]
})

# Concatenate theo axis=1
combined = pd.concat([df1, df2], axis=1)
print("Sau khi nối theo columns:")
print(combined)
```

**Ví dụ thực tế**: Gộp dữ liệu nhiều tháng
```python
# Dữ liệu bán hàng từ nhiều file/tháng
jan_sales = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=5, freq="D"),
    "Product": ["Laptop", "Mouse", "Keyboard", "Laptop", "Monitor"],
    "Revenue": [15000000, 500000, 2000000, 15000000, 5000000]
})

feb_sales = pd.DataFrame({
    "Date": pd.date_range("2024-02-01", periods=5, freq="D"),
    "Product": ["Laptop", "Mouse", "Keyboard", "Laptop", "Monitor"],
    "Revenue": [18000000, 600000, 2500000, 18000000, 6000000]
})

# Gộp dữ liệu
all_sales = pd.concat([jan_sales, feb_sales], ignore_index=True)

# Phân tích
monthly_summary = all_sales.groupby([all_sales["Date"].dt.month, "Product"])["Revenue"].sum()
print("Tổng hợp theo tháng và sản phẩm:")
print(monthly_summary)
```

## 5. Xử Lý Conflicts Khi Merge

### Suffixes (Hậu Tố)

Khi hai bảng có columns trùng tên (không phải key):

```python
# Hai bảng có column "Name"
customers = pd.DataFrame({
    "ID": [1, 2, 3],
    "Name": ["Nguyễn Văn A", "Trần Thị B", "Lê Văn C"],
    "City": ["Hà Nội", "TP.HCM", "Đà Nẵng"]
})

orders = pd.DataFrame({
    "ID": [1, 2, 3],
    "Name": ["A Company", "B Company", "C Company"],  # Tên khác
    "Amount": [1000000, 2000000, 1500000]
})

# Merge với suffixes
result = pd.merge(customers, orders, on="ID", suffixes=("_Customer", "_Company"))
print("Merge với suffixes:")
print(result)
```

### Drop Duplicate Columns

```python
# Nếu không cần một trong hai columns trùng
result = pd.merge(customers, orders, on="ID", suffixes=("_Customer", "_Company"))
result = result.drop(columns=["Name_Company"])  # Xóa column không cần
result = result.rename(columns={"Name_Customer": "Name"})  # Đổi tên lại
```

## 6. Merge Nhiều DataFrames

```python
# Merge nhiều bảng
customers = pd.DataFrame({
    "CustomerID": [1, 2, 3],
    "Name": ["A", "B", "C"]
})

orders = pd.DataFrame({
    "OrderID": [101, 102, 103],
    "CustomerID": [1, 2, 3],
    "Amount": [1000000, 2000000, 1500000]
})

products = pd.DataFrame({
    "ProductID": [1, 2, 3],
    "ProductName": ["Laptop", "Mouse", "Keyboard"]
})

order_details = pd.DataFrame({
    "OrderID": [101, 102, 103],
    "ProductID": [1, 2, 3],
    "Quantity": [1, 2, 1]
})

# Merge từng bước
step1 = pd.merge(customers, orders, on="CustomerID")
step2 = pd.merge(step1, order_details, on="OrderID")
final = pd.merge(step2, products, on="ProductID")

print("Kết quả merge nhiều bảng:")
print(final)
```

## 7. Merge Với Index

```python
# Merge sử dụng index
customers = pd.DataFrame({
    "Name": ["A", "B", "C"],
    "City": ["Hà Nội", "TP.HCM", "Đà Nẵng"]
}, index=[1, 2, 3])  # Index là CustomerID

orders = pd.DataFrame({
    "Amount": [1000000, 2000000, 1500000],
    "Date": ["2024-01-01", "2024-01-02", "2024-01-03"]
}, index=[1, 2, 3])

# Merge với left_index và right_index
result = pd.merge(customers, orders, left_index=True, right_index=True)
print("Merge với index:")
print(result)
```

## 8. Case Study: Phân Tích Đơn Hàng Hoàn Chỉnh

```python
# Tạo dữ liệu mẫu
customers = pd.DataFrame({
    "CustomerID": [1, 2, 3, 4, 5],
    "Name": ["Nguyễn Văn A", "Trần Thị B", "Lê Văn C", "Phạm Thị D", "Hoàng Văn E"],
    "City": ["Hà Nội", "TP.HCM", "Đà Nẵng", "Hà Nội", "TP.HCM"],
    "Segment": ["VIP", "Gold", "Silver", "Gold", "Silver"]
})

products = pd.DataFrame({
    "ProductID": [1, 2, 3, 4],
    "ProductName": ["Laptop", "Mouse", "Keyboard", "Monitor"],
    "Category": ["Electronics", "Accessories", "Accessories", "Electronics"],
    "Price": [15000000, 500000, 2000000, 5000000]
})

orders = pd.DataFrame({
    "OrderID": [101, 102, 103, 104, 105],
    "CustomerID": [1, 2, 3, 1, 4],
    "OrderDate": ["2024-01-01", "2024-01-02", "2024-01-03", "2024-01-04", "2024-01-05"]
})

order_details = pd.DataFrame({
    "OrderDetailID": [1, 2, 3, 4, 5, 6],
    "OrderID": [101, 101, 102, 103, 104, 105],
    "ProductID": [1, 2, 1, 3, 4, 2],
    "Quantity": [1, 2, 1, 1, 2, 3],
    "Discount": [0.1, 0.05, 0, 0.15, 0.1, 0]
})

# Bước 1: Merge order_details với products
order_with_products = pd.merge(order_details, products, on="ProductID")

# Bước 2: Tính tổng tiền
order_with_products["Subtotal"] = order_with_products["Price"] * order_with_products["Quantity"]
order_with_products["Discount_Amount"] = order_with_products["Subtotal"] * order_with_products["Discount"]
order_with_products["Total"] = order_with_products["Subtotal"] - order_with_products["Discount_Amount"]

# Bước 3: Tổng hợp theo đơn hàng
order_totals = order_with_products.groupby("OrderID").agg({
    "Total": "sum",
    "Quantity": "sum"
}).reset_index()

# Bước 4: Merge với orders và customers
final_report = pd.merge(orders, order_totals, on="OrderID")
final_report = pd.merge(final_report, customers, on="CustomerID")

print("BÁO CÁO ĐƠN HÀNG HOÀN CHỈNH")
print("-" * 60)
print(final_report[["OrderID", "Name", "City", "Segment", "Total", "Quantity"]])

# Phân tích theo segment
segment_analysis = final_report.groupby("Segment").agg({
    "OrderID": "count",
    "Total": "sum",
    "Quantity": "sum"
})

print("\nPhân tích theo Segment:")
print(segment_analysis)
```

## Bài Tập Thực Hành

### Bài 1: Merge Nhiều Bảng
```python
# Tạo 3 bảng:
# 1. Customers (CustomerID, Name, City)
# 2. Orders (OrderID, CustomerID, Date, Amount)
# 3. Products (ProductID, ProductName, Price)

# Merge và tính:
# - Tổng đơn hàng mỗi khách hàng
# - Top 3 khách hàng có tổng đơn hàng cao nhất
```

### Bài 2: Phân Tích Doanh Số
```python
# Merge dữ liệu bán hàng từ nhiều nguồn:
# - Bảng sản phẩm
# - Bảng đơn hàng
# - Bảng khách hàng

# Tính:
# - Doanh số theo sản phẩm
# - Doanh số theo khách hàng
# - Doanh số theo thành phố
```

### Bài 3: Gộp Dữ Liệu Nhiều Tháng
```python
# Có dữ liệu bán hàng từ 3 tháng (3 files/DataFrames)
# Gộp lại và phân tích:
# - Tổng doanh số từng tháng
# - Tăng trưởng từng tháng
# - Top sản phẩm bán chạy nhất
```

## Tổng Kết

✅ Đã học:
- Các loại joins (inner, left, right, outer)
- Merge với một hoặc nhiều keys
- Merge với keys khác tên
- Concatenate DataFrames
- Xử lý conflicts
- Merge nhiều DataFrames

**Tiếp theo**: [Phần 3: Trực Quan Hóa Dữ Liệu](03-data-visualization/01-matplotlib-basics.md)

