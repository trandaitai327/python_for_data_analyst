# 01. Descriptive Statistics

## Mục Tiêu
- Tính các thống kê mô tả: mean, median, mode, std
- Hiểu về phân bố dữ liệu và các dạng phân bố hay gặp
- Phân biệt được population vs sample, parameter vs statistic
- Phát hiện skewness và kurtosis và hiểu ý nghĩa trực quan
- Sử dụng pandas và scipy cho thống kê mô tả phục vụ phân tích dữ liệu thực tế

## Descriptive Statistics Là Gì?

**Descriptive statistics (thống kê mô tả)** là tập hợp các công cụ giúp bạn:
- **Tóm tắt dữ liệu**: Mean, median, mode
- **Đo lường độ phân tán**: Std, variance, range, IQR
- **Hiểu hình dạng phân bố**: Skewness, kurtosis
- **So sánh nhóm**: Group statistics theo nhiều chiều (product, region, segment,…)

Khác với **inferential statistics (thống kê suy luận)**, thống kê mô tả:
- Không cố gắng suy luận cho toàn bộ population
- Tập trung mô tả chính xác sample hiện có
- Là bước **bắt buộc** trước khi làm bất kỳ mô hình hay kiểm định nào

### Population vs Sample (Tổng Thể vs Mẫu)

- **Population**: Toàn bộ tập đối tượng bạn quan tâm (tất cả khách hàng, tất cả đơn hàng năm 2024,…)
- **Sample**: Một phần rút ra từ population (1.000 khách hàng được survey,…)
- **Parameter**: Giá trị thật của population (μ, σ², p,…) – thường không biết
- **Statistic**: Giá trị tính từ sample (x̄, s², p̂,…) – dùng để ước lượng parameter

Trong thực tế Data Analyst gần như **luôn** làm việc với sample → các thống kê bạn tính (mean, std,…) là **estimates** chứ không phải giá trị tuyệt đối.

## 1. Central Tendency (Xu Hướng Trung Tâm)

### Mean (Trung Bình)

Với dữ liệu \(x_1, x_2, ..., x_n\), **mean** được định nghĩa:
\[
\bar{x} = \frac{1}{n} \sum_{i=1}^{n} x_i
\]

- Nhạy cảm với **outliers**
- Phù hợp khi phân bố không quá lệch, không có giá trị cực đoan

```python
import pandas as pd
import numpy as np
from scipy import stats

# Dữ liệu doanh số
sales = [50000000, 55000000, 60000000, 65000000, 70000000, 75000000, 80000000]

# Mean
mean_sales = np.mean(sales)
print(f"Trung bình: {mean_sales:,.0f} VND")

# Mean với pandas
df = pd.DataFrame({"Sales": sales})
print(f"Trung bình (pandas): {df['Sales'].mean():,.0f} VND")
```

### Median (Trung Vị)

Giá trị ở giữa khi sắp xếp dữ liệu.

Tính chất:
- Ít bị ảnh hưởng bởi **outliers** và phân bố lệch
- Thường được dùng để mô tả **thu nhập**, **giá nhà**,… (dữ liệu rất hay lệch phải)

```python
# Median
median_sales = np.median(sales)
print(f"Trung vị: {median_sales:,.0f} VND")

# Median với pandas
print(f"Trung vị (pandas): {df['Sales'].median():,.0f} VND")

# Median ít bị ảnh hưởng bởi outliers
sales_with_outlier = sales + [500000000]  # Thêm outlier
print(f"Mean với outlier: {np.mean(sales_with_outlier):,.0f} VND")
print(f"Median với outlier: {np.median(sales_with_outlier):,.0f} VND")
```

### Mode (Mốt)

Giá trị xuất hiện nhiều nhất.

Mode hữu ích khi:
- Dữ liệu là **categorical** (loại sản phẩm phổ biến nhất,…)
- Dữ liệu numeric nhưng có nhiều giá trị trùng nhau (ví dụ score, rating,…)

```python
# Mode
sales_with_mode = [50, 55, 60, 60, 65, 70, 60]
mode_sales = stats.mode(sales_with_mode)
print(f"Mốt: {mode_sales.mode[0]}, Tần suất: {mode_sales.count[0]}")

# Mode với pandas
df_mode = pd.DataFrame({"Sales": sales_with_mode})
print(f"Mốt (pandas): {df_mode['Sales'].mode().values}")
```

**Ví dụ thực tế**: So sánh mean và median
```python
# Doanh số có outliers
sales_data = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker", "Tablet"],
    "Sales": [50000000, 55000000, 60000000, 65000000, 70000000, 500000000]  # Tablet là outlier
})

print("Thống kê doanh số:")
print(f"Mean: {sales_data['Sales'].mean():,.0f} VND")
print(f"Median: {sales_data['Sales'].median():,.0f} VND")
print(f"Mode: {sales_data['Sales'].mode().values}")

# Khi có outlier, median đại diện tốt hơn
print("\n→ Median ít bị ảnh hưởng bởi outliers")
```

## 2. Measures of Dispersion (Độ Phân Tán)

Các chỉ số độ phân tán cho biết **dữ liệu “vung vãi” quanh trung tâm thế nào**:
- Nếu độ phân tán nhỏ → dữ liệu ổn định, ít biến động
- Nếu độ phân tán lớn → dữ liệu biến động mạnh, rủi ro cao hơn

### Standard Deviation (Độ Lệch Chuẩn)

Với mẫu \(x_1, x_2, ..., x_n\), sample standard deviation:
\[
s = \sqrt{\frac{1}{n-1} \sum_{i=1}^{n}(x_i - \bar{x})^2}
\]

- Std càng lớn → dữ liệu càng phân tán
- Thường đi kèm mean để mô tả dữ liệu: “Doanh số trung bình 50M ± 10M”

```python
# Standard deviation
std_sales = np.std(sales)
print(f"Độ lệch chuẩn: {std_sales:,.0f} VND")

# Sample std (chia cho n-1)
std_sample = np.std(sales, ddof=1)
print(f"Độ lệch chuẩn mẫu: {std_sample:,.0f} VND")

# Với pandas (mặc định là sample std)
print(f"Std (pandas): {df['Sales'].std():,.0f} VND")
```

### Variance (Phương Sai)

Phương sai là **bình phương của độ lệch chuẩn**:
\[
s^2 = \frac{1}{n-1} \sum_{i=1}^{n}(x_i - \bar{x})^2
\]

- Đơn vị là “bình phương” của đơn vị gốc → khó diễn giải trực quan
- Thường dùng nhiều trong các công thức toán, mô hình (ví dụ: phân phối chuẩn, ANOVA,…)

```python
# Variance
var_sales = np.var(sales)
print(f"Phương sai: {var_sales:,.0f}")

# Variance = std^2
print(f"Kiểm tra: {std_sales**2:,.0f}")
```

### Range (Khoảng)

```python
# Range
range_sales = np.max(sales) - np.min(sales)
print(f"Khoảng: {range_sales:,.0f} VND")

# Quartiles
q1 = np.percentile(sales, 25)
q2 = np.percentile(sales, 50)  # Median
q3 = np.percentile(sales, 75)
iqr = q3 - q1

print(f"Q1 (25%): {q1:,.0f} VND")
print(f"Q2 (50%): {q2:,.0f} VND")
print(f"Q3 (75%): {q3:,.0f} VND")
print(f"IQR: {iqr:,.0f} VND")
```

**Ví dụ thực tế**: Phân tích độ phân tán
```python
# So sánh độ phân tán giữa 2 nhóm
group_a = np.random.normal(50000000, 5000000, 100)
group_b = np.random.normal(50000000, 15000000, 100)

print("So sánh độ phân tán:")
print(f"Group A - Mean: {np.mean(group_a):,.0f}, Std: {np.std(group_a):,.0f}")
print(f"Group B - Mean: {np.mean(group_b):,.0f}, Std: {np.std(group_b):,.0f}")
print("\n→ Group B có độ phân tán lớn hơn (std cao hơn)")
```

## 3. Distribution (Phân Bố)

Dữ liệu thực tế thường:
- Không hoàn toàn chuẩn (normal)
- Có thể lệch phải (right-skewed) hoặc lệch trái (left-skewed)

Hiểu phân bố giúp bạn:
- Chọn thước đo trung tâm phù hợp (mean vs median)
- Chọn phương pháp thống kê (parametric vs non-parametric)
- Nhận diện dữ liệu “bẩn” hoặc bất thường

### Skewness (Độ Lệch)

```python
from scipy.stats import skew, kurtosis

# Tạo dữ liệu
normal_data = np.random.normal(50000000, 10000000, 1000)
skewed_data = np.random.exponential(50000000, 1000)

# Skewness
skew_normal = skew(normal_data)
skew_skewed = skew(skewed_data)

print(f"Skewness (normal): {skew_normal:.2f}")
print(f"Skewness (skewed): {skew_skewed:.2f}")
print("\n→ Skewness > 0: lệch phải, < 0: lệch trái, ≈ 0: đối xứng")
```

### Kurtosis (Độ Nhọn)

```python
# Kurtosis
kurt_normal = kurtosis(normal_data)
kurt_skewed = kurtosis(skewed_data)

print(f"Kurtosis (normal): {kurt_normal:.2f}")
print(f"Kurtosis (skewed): {kurt_skewed:.2f}")
print("\n→ Kurtosis > 3: nhọn hơn, < 3: phẳng hơn normal distribution")
```

## 4. Descriptive Statistics với Pandas

### Describe()

```python
# Tạo dữ liệu
sales_data = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"] * 20,
    "Sales": np.random.normal(50000000, 10000000, 100),
    "Price": np.random.randint(500000, 20000000, 100),
    "Quantity": np.random.randint(1, 100, 100)
})

# Describe
print("Thống kê mô tả:")
print(sales_data.describe())

# Describe với percentiles tùy chỉnh
print("\nVới percentiles tùy chỉnh:")
print(sales_data.describe(percentiles=[0.1, 0.25, 0.5, 0.75, 0.9]))
```

### Group Statistics

```python
# Thống kê theo group
print("Thống kê theo sản phẩm:")
print(sales_data.groupby("Product")["Sales"].describe())

# Nhiều statistics
print("\nNhiều statistics:")
print(sales_data.groupby("Product").agg({
    "Sales": ["mean", "median", "std", "min", "max"],
    "Price": ["mean", "std"],
    "Quantity": "sum"
}))
```

## 5. Z-Score (Chuẩn Hóa)

**Z-score** cho biết **một điểm nằm cách mean bao nhiêu độ lệch chuẩn**:
\[
z = \frac{x - \bar{x}}{s}
\]

- \(|z| > 2\): thường xem là giá trị **bất thường nhẹ**
- \(|z| > 3\): thường xem là **outlier mạnh**

Ứng dụng:
- Phát hiện giá trị bất thường trong doanh số, lượng truy cập, số lần login,…
- Chuẩn hóa dữ liệu trước khi đưa vào model (đặc biệt với các thuật toán nhạy scale)

```python
# Z-score: (x - mean) / std
z_scores = stats.zscore(sales_data["Sales"])
sales_data["Sales_ZScore"] = z_scores

print("Z-scores:")
print(sales_data[["Product", "Sales", "Sales_ZScore"]].head(10))

# Phát hiện outliers (|z-score| > 3)
outliers = sales_data[abs(sales_data["Sales_ZScore"]) > 3]
print(f"\nSố outliers: {len(outliers)}")
```

## 6. Correlation (Tương Quan)

Correlation đo lường **mức độ tuyến tính** giữa các biến:
- Gần +1: quan hệ tuyến tính dương mạnh
- Gần -1: quan hệ tuyến tính âm mạnh
- Gần 0: ít hoặc không có quan hệ tuyến tính

⚠️ **Lưu ý quan trọng**: *Correlation ≠ Causation*  
Hai biến có tương quan mạnh **không có nghĩa** là một biến gây ra biến kia. Cần thêm phân tích business, thiết kế experiment, hoặc kiểm định nhân quả.

```python
# Correlation matrix
correlation = sales_data[["Sales", "Price", "Quantity"]].corr()
print("Correlation Matrix:")
print(correlation)

# Correlation giữa 2 biến
corr_sales_price = sales_data["Sales"].corr(sales_data["Price"])
print(f"\nCorrelation (Sales, Price): {corr_sales_price:.3f}")
```

## 7. Case Study: Phân Tích Doanh Số

```python
# Tạo dữ liệu doanh số
np.random.seed(42)
sales_analysis = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=365, freq="D"),
    "Product": np.random.choice(["Laptop", "Mouse", "Keyboard"], 365),
    "Sales": np.random.normal(50000000, 10000000, 365),
    "Region": np.random.choice(["North", "South", "Central"], 365)
})

# Thống kê tổng quan
print("THỐNG KÊ TỔNG QUAN")
print("=" * 50)
print(f"Tổng số records: {len(sales_analysis)}")
print(f"Mean: {sales_analysis['Sales'].mean():,.0f} VND")
print(f"Median: {sales_analysis['Sales'].median():,.0f} VND")
print(f"Std: {sales_analysis['Sales'].std():,.0f} VND")
print(f"Min: {sales_analysis['Sales'].min():,.0f} VND")
print(f"Max: {sales_analysis['Sales'].max():,.0f} VND")
print(f"Skewness: {skew(sales_analysis['Sales']):.2f}")
print(f"Kurtosis: {kurtosis(sales_analysis['Sales']):.2f}")

# Thống kê theo nhóm
print("\nTHỐNG KÊ THEO SẢN PHẨM")
print("=" * 50)
product_stats = sales_analysis.groupby("Product")["Sales"].agg([
    "count", "mean", "median", "std", "min", "max"
])
print(product_stats)

# Thống kê theo region
print("\nTHỐNG KÊ THEO KHU VỰC")
print("=" * 50)
region_stats = sales_analysis.groupby("Region")["Sales"].agg([
    "mean", "median", "std"
])
print(region_stats)
```

## Bài Tập Thực Hành

### Bài 1: Phân Tích Thống Kê
```python
# Tạo dữ liệu doanh số 12 tháng
# Tính:
# 1. Mean, median, mode
# 2. Std, variance, range, IQR
# 3. Skewness và kurtosis
# 4. Z-scores và phát hiện outliers
```

### Bài 2: So Sánh Nhóm
```python
# So sánh doanh số giữa các:
# - Sản phẩm
# - Khu vực
# - Tháng
# Sử dụng groupby và describe
```

### Bài 3: Correlation Analysis
```python
# Phân tích correlation giữa:
# - Price và Sales
# - Quantity và Revenue
# - Tạo correlation matrix
```

## Tổng Kết

✅ Đã học:
- Central tendency (mean, median, mode)
- Measures of dispersion (std, variance, range, IQR)
- Distribution (skewness, kurtosis)
- Z-scores
- Correlation
- Group statistics

**Tiếp theo**: [02. Hypothesis Testing](02-hypothesis-testing.md)

