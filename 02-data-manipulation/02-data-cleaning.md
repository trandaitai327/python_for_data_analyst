# 02. Data Cleaning

## Mục Tiêu
- Xử lý missing values (giá trị thiếu)
- Xử lý duplicates (dữ liệu trùng lặp)
- Xử lý outliers (giá trị ngoại lai)
- Chuyển đổi kiểu dữ liệu
- Chuẩn hóa dữ liệu

## Tại Sao Cần Làm Sạch Dữ Liệu?

Dữ liệu thực tế thường có vấn đề:
- ❌ Missing values (NULL, NaN, empty)
- ❌ Duplicates (trùng lặp)
- ❌ Inconsistent formats (định dạng không nhất quán)
- ❌ Outliers (giá trị bất thường)
- ❌ Wrong data types (sai kiểu dữ liệu)

**Quy tắc**: Garbage in, garbage out - Dữ liệu xấu → Kết quả xấu

## 1. Xử Lý Missing Values

### Phát Hiện Missing Values

```python
import pandas as pd
import numpy as np

# Tạo dữ liệu mẫu có missing values
data = {
    "Name": ["Nguyễn Văn A", "Trần Thị B", None, "Lê Văn C", "Phạm Thị D"],
    "Age": [25, 30, np.nan, 35, None],
    "Salary": [10000000, 15000000, 12000000, np.nan, 18000000],
    "Department": ["Sales", "IT", "Sales", None, "IT"]
}

df = pd.DataFrame(data)
print(df)

# Kiểm tra missing values
print("\nMissing values:")
print(df.isnull())  # True nếu là missing
print(df.isnull().sum())  # Đếm missing values mỗi column
print(df.isnull().sum() / len(df) * 100)  # Phần trăm missing
```

### Xử Lý Missing Values

#### Option 1: Xóa (Drop)

```python
# Xóa rows có bất kỳ missing value nào
df_dropped = df.dropna()
print("Sau khi xóa rows có missing:")
print(df_dropped)

# Xóa rows có tất cả values là missing
df_dropped = df.dropna(how='all')

# Xóa rows có missing ở column cụ thể
df_dropped = df.dropna(subset=['Age', 'Salary'])

# Xóa columns có missing
df_dropped = df.dropna(axis=1)  # axis=1 là columns
```

**Khi nào dùng**: Khi missing values ít và không quan trọng

#### Option 2: Điền Giá Trị (Fill)

```python
# Điền giá trị cố định
df_filled = df.fillna(0)  # Điền 0
df_filled = df.fillna("Unknown")  # Điền text

# Điền giá trị thống kê
df_filled = df.fillna(df.mean())  # Điền trung bình (chỉ số)
df_filled = df['Age'].fillna(df['Age'].mean())
df_filled = df['Age'].fillna(df['Age'].median())  # Điền median
df_filled = df['Age'].fillna(df['Age'].mode()[0])  # Điền mode

# Điền forward fill (lấy giá trị trước đó)
df_filled = df.ffill()  # hoặc df.fillna(method='ffill') trong pandas cũ

# Điền backward fill (lấy giá trị sau đó)
df_filled = df.bfill()  # hoặc df.fillna(method='bfill') trong pandas cũ
```

**Ví dụ thực tế**: Xử lý dữ liệu nhân viên
```python
# Dữ liệu nhân viên có missing values
employees = pd.DataFrame({
    "Name": ["Nguyễn Văn A", "Trần Thị B", None, "Lê Văn C"],
    "Age": [25, 30, np.nan, 35],
    "Salary": [10000000, 15000000, np.nan, 20000000],
    "Department": ["Sales", "IT", "Sales", None]
})

# Chiến lược xử lý:
# 1. Name: Điền "Unknown"
employees["Name"] = employees["Name"].fillna("Unknown")

# 2. Age: Điền trung bình
employees["Age"] = employees["Age"].fillna(employees["Age"].mean())

# 3. Salary: Điền median (ít bị ảnh hưởng bởi outliers)
employees["Salary"] = employees["Salary"].fillna(employees["Salary"].median())

# 4. Department: Điền mode (giá trị xuất hiện nhiều nhất)
employees["Department"] = employees["Department"].fillna(
    employees["Department"].mode()[0] if not employees["Department"].mode().empty else "Unknown"
)

print("Sau khi xử lý missing values:")
print(employees)
```

## 2. Xử Lý Duplicates

### Phát Hiện Duplicates

```python
# Tạo dữ liệu có duplicates
data = {
    "ID": [1, 2, 3, 2, 4, 3],
    "Name": ["A", "B", "C", "B", "D", "C"],
    "Sales": [100000, 200000, 150000, 200000, 300000, 150000]
}

df = pd.DataFrame(data)
print(df)

# Kiểm tra duplicates
print("\nDuplicates:")
print(df.duplicated())  # True nếu row trùng
print(df.duplicated().sum())  # Số lượng duplicates

# Kiểm tra duplicates theo column cụ thể
print(df.duplicated(subset=['ID']))
```

### Xóa Duplicates

```python
# Xóa tất cả duplicates (giữ row đầu tiên)
df_unique = df.drop_duplicates()
print("Sau khi xóa duplicates:")
print(df_unique)

# Xóa duplicates, giữ row cuối cùng
df_unique = df.drop_duplicates(keep='last')

# Xóa duplicates theo column cụ thể
df_unique = df.drop_duplicates(subset=['ID'])

# Xóa tất cả rows trùng (không giữ cái nào)
df_unique = df.drop_duplicates(keep=False)
```

**Ví dụ thực tế**: Làm sạch dữ liệu khách hàng
```python
# Dữ liệu khách hàng có thể trùng
customers = pd.DataFrame({
    "CustomerID": [1, 2, 3, 2, 4, 3, 5],
    "Name": ["Nguyễn Văn A", "Trần Thị B", "Lê Văn C", 
             "Trần Thị B", "Phạm Thị D", "Lê Văn C", "Hoàng Văn E"],
    "Email": ["a@email.com", "b@email.com", "c@email.com",
              "b@email.com", "d@email.com", "c@email.com", "e@email.com"],
    "Total_Purchase": [1000000, 2000000, 1500000, 2500000, 3000000, 1500000, 500000]
})

print("Trước khi xử lý:")
print(f"Số khách hàng: {len(customers)}")
print(f"Số duplicates: {customers.duplicated(subset=['CustomerID']).sum()}")

# Xóa duplicates, giữ bản có Total_Purchase cao hơn
customers = customers.sort_values('Total_Purchase', ascending=False)
customers_clean = customers.drop_duplicates(subset=['CustomerID'], keep='first')

print("\nSau khi xử lý:")
print(f"Số khách hàng: {len(customers_clean)}")
print(customers_clean)
```

## 3. Xử Lý Outliers

### Phát Hiện Outliers

```python
# Tạo dữ liệu có outliers
sales = pd.DataFrame({
    "Product": ["A", "B", "C", "D", "E", "F", "G"],
    "Sales": [100000, 120000, 150000, 180000, 200000, 5000000, 220000]  # 5000000 là outlier
})

# Phương pháp 1: IQR (Interquartile Range)
Q1 = sales['Sales'].quantile(0.25)
Q3 = sales['Sales'].quantile(0.75)
IQR = Q3 - Q1

lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

print(f"Q1: {Q1:,.0f}")
print(f"Q3: {Q3:,.0f}")
print(f"IQR: {IQR:,.0f}")
print(f"Lower bound: {lower_bound:,.0f}")
print(f"Upper bound: {upper_bound:,.0f}")

outliers = sales[(sales['Sales'] < lower_bound) | (sales['Sales'] > upper_bound)]
print("\nOutliers:")
print(outliers)

# Phương pháp 2: Z-score
from scipy import stats
z_scores = np.abs(stats.zscore(sales['Sales']))
outliers_z = sales[z_scores > 3]
print("\nOutliers (Z-score > 3):")
print(outliers_z)
```

### Xử Lý Outliers

```python
# Option 1: Xóa outliers
sales_clean = sales[(sales['Sales'] >= lower_bound) & (sales['Sales'] <= upper_bound)]

# Option 2: Capping (giới hạn giá trị)
sales_capped = sales.copy()
sales_capped.loc[sales_capped['Sales'] > upper_bound, 'Sales'] = upper_bound
sales_capped.loc[sales_capped['Sales'] < lower_bound, 'Sales'] = lower_bound

# Option 3: Thay bằng median
median_sales = sales['Sales'].median()
sales_median = sales.copy()
sales_median.loc[(sales_median['Sales'] < lower_bound) | 
                 (sales_median['Sales'] > upper_bound), 'Sales'] = median_sales
```

**Ví dụ thực tế**: Xử lý outliers trong doanh số
```python
# Dữ liệu doanh số có outliers
monthly_sales = pd.DataFrame({
    "Month": ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug"],
    "Sales": [50000000, 55000000, 60000000, 65000000, 
              70000000, 500000000, 75000000, 80000000]  # Jun có outlier
})

# Phát hiện outliers
Q1 = monthly_sales['Sales'].quantile(0.25)
Q3 = monthly_sales['Sales'].quantile(0.75)
IQR = Q3 - Q1
upper_bound = Q3 + 1.5 * IQR

print(f"Upper bound: {upper_bound:,.0f}")

# Xử lý: Thay outlier bằng median của các tháng khác
median_normal = monthly_sales[monthly_sales['Sales'] <= upper_bound]['Sales'].median()
monthly_sales.loc[monthly_sales['Sales'] > upper_bound, 'Sales'] = median_normal

print("\nSau khi xử lý outliers:")
print(monthly_sales)
```

## 4. Chuyển Đổi Kiểu Dữ Liệu

```python
# Tạo dữ liệu với kiểu sai
data = {
    "ID": ["1", "2", "3", "4"],  # String thay vì int
    "Price": ["1000000", "1500000", "2000000", "2500000"],  # String
    "Date": ["2024-01-01", "2024-01-02", "2024-01-03", "2024-01-04"],  # String
    "IsActive": ["True", "False", "True", "False"]  # String
}

df = pd.DataFrame(data)
print("Kiểu dữ liệu ban đầu:")
print(df.dtypes)

# Chuyển đổi
df["ID"] = df["ID"].astype(int)
df["Price"] = df["Price"].astype(float)
df["Date"] = pd.to_datetime(df["Date"])
df["IsActive"] = df["IsActive"].astype(bool)

print("\nKiểu dữ liệu sau khi chuyển đổi:")
print(df.dtypes)
```

**Ví dụ thực tế**: Chuẩn hóa dữ liệu
```python
# Dữ liệu từ form (có thể có lỗi format)
raw_data = pd.DataFrame({
    "OrderID": ["001", "002", "003", "004"],
    "Amount": ["1,000,000", "1.5 triệu", "2000000", "2,500,000"],
    "Date": ["01/01/2024", "2024-01-02", "01-03-2024", "2024/01/04"]
})

# Chuẩn hóa Amount
def clean_amount(amount):
    # Loại bỏ dấu phẩy và chữ
    amount = str(amount).replace(",", "").replace("triệu", "000000")
    try:
        return float(amount)
    except:
        return None

raw_data["Amount_Clean"] = raw_data["Amount"].apply(clean_amount)

# Chuẩn hóa Date (thử nhiều format)
# Lưu ý: không thể dùng cả format và dayfirst cùng lúc
raw_data["Date_Clean"] = pd.to_datetime(raw_data["Date"], errors='coerce', dayfirst=True)

print("Sau khi chuẩn hóa:")
print(raw_data[["OrderID", "Amount_Clean", "Date_Clean"]])
```

## 5. Chuẩn Hóa Dữ Liệu

### Chuẩn Hóa Text

```python
# Dữ liệu text không nhất quán
products = pd.DataFrame({
    "Product": ["  LAPTOP  ", "mouse", "Keyboard", "  MONITOR  "],
    "Category": ["Electronics", "electronics", "ELECTRONICS", "Electronics"]
})

# Chuẩn hóa: trim và title case
products["Product"] = products["Product"].str.strip().str.title()
products["Category"] = products["Category"].str.strip().str.title()

print("Sau khi chuẩn hóa:")
print(products)
```

### Chuẩn Hóa Số

```python
# Dữ liệu số có đơn vị khác nhau
prices = pd.DataFrame({
    "Product": ["A", "B", "C"],
    "Price": ["1 triệu", "1.5 triệu", "2000000"]
})

def normalize_price(price_str):
    price_str = str(price_str).lower().replace(",", "")
    if "triệu" in price_str:
        return float(price_str.replace("triệu", "").strip()) * 1000000
    else:
        return float(price_str)

prices["Price_Normalized"] = prices["Price"].apply(normalize_price)
print(prices)
```

## Quy Trình Làm Sạch Dữ Liệu Hoàn Chỉnh

```python
def clean_dataframe(df):
    """Hàm tổng hợp để làm sạch DataFrame"""
    
    # 1. Xử lý missing values
    # Điền số bằng median
    numeric_cols = df.select_dtypes(include=[np.number]).columns
    for col in numeric_cols:
        df[col] = df[col].fillna(df[col].median())
    
    # Điền text bằng mode
    text_cols = df.select_dtypes(include=['object']).columns
    for col in text_cols:
        mode_value = df[col].mode()
        if not mode_value.empty:
            df[col] = df[col].fillna(mode_value[0])
    
    # 2. Xóa duplicates
    df = df.drop_duplicates()
    
    # 3. Xử lý outliers (capping)
    for col in numeric_cols:
        Q1 = df[col].quantile(0.25)
        Q3 = df[col].quantile(0.75)
        IQR = Q3 - Q1
        lower = Q1 - 1.5 * IQR
        upper = Q3 + 1.5 * IQR
        df[col] = df[col].clip(lower=lower, upper=upper)
    
    # 4. Chuẩn hóa text
    for col in text_cols:
        df[col] = df[col].astype(str).str.strip().str.title()
    
    return df

# Sử dụng
dirty_data = pd.DataFrame({
    "Name": ["  nguyễn văn a  ", "Trần Thị B", None, "trần thị b"],
    "Age": [25, 30, None, 30],
    "Salary": [10000000, 15000000, 500000000, 15000000]  # Outlier
})

clean_data = clean_dataframe(dirty_data.copy())
print("Dữ liệu sạch:")
print(clean_data)
```

## Bài Tập Thực Hành

### Bài 1: Làm Sạch Dữ Liệu Khách Hàng
```python
# Tạo DataFrame với:
# - Missing values
# - Duplicates
# - Inconsistent text format
# - Outliers trong số tiền

# Áp dụng tất cả kỹ thuật làm sạch
```

### Bài 2: Xử Lý Dữ Liệu Bán Hàng
```python
# Dữ liệu có:
# - Giá trị âm (lỗi)
# - Ngày tháng format khác nhau
# - Số tiền có đơn vị khác nhau (triệu, nghìn)

# Chuẩn hóa tất cả
```

## Tổng Kết

✅ Đã học:
- Phát hiện và xử lý missing values
- Xử lý duplicates
- Phát hiện và xử lý outliers
- Chuyển đổi kiểu dữ liệu
- Chuẩn hóa dữ liệu

**Tiếp theo**: [03. Data Transformation](03-data-transformation.md)

