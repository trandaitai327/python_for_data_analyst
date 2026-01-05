# 02. Seaborn

## Mục Tiêu
- Sử dụng Seaborn cho statistical visualizations
- Tạo biểu đồ đẹp với ít code hơn
- Heatmaps, box plots, violin plots
- Tùy chỉnh style và palette

## Seaborn Là Gì?

Seaborn là thư viện visualization dựa trên Matplotlib:
- **Đẹp hơn**: Style mặc định đẹp hơn
- **Dễ dùng**: Ít code hơn, tự động hóa nhiều thứ
- **Statistical**: Tập trung vào statistical visualizations
- **Tích hợp Pandas**: Làm việc tốt với DataFrame

## Cài Đặt

```bash
pip install seaborn
```

## 1. Style và Setup

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Set style
sns.set_style("whitegrid")
sns.set_palette("husl")

# Hoặc
sns.set_style("darkgrid", {"axes.facecolor": ".9"})
```

## 2. Line Plot

```python
# Tạo dữ liệu
dates = pd.date_range("2024-01-01", periods=12, freq="M")
sales_data = pd.DataFrame({
    "Date": dates,
    "Laptop": [30 + i*2 + np.random.randint(-2, 2) for i in range(12)],
    "Mouse": [150 + i*5 + np.random.randint(-5, 5) for i in range(12)],
    "Keyboard": [80 + i*3 + np.random.randint(-3, 3) for i in range(12)]
})

# Line plot
plt.figure(figsize=(12, 6))
sns.lineplot(data=sales_data, x="Date", y="Laptop", label="Laptop", marker='o')
sns.lineplot(data=sales_data, x="Date", y="Mouse", label="Mouse", marker='s')
sns.lineplot(data=sales_data, x="Date", y="Keyboard", label="Keyboard", marker='^')
plt.title("Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: Xu hướng doanh số
```python
# Dữ liệu doanh số với nhiều sản phẩm
sales_long = sales_data.melt(id_vars="Date", 
                             value_vars=["Laptop", "Mouse", "Keyboard"],
                             var_name="Product", 
                             value_name="Sales")

plt.figure(figsize=(12, 6))
sns.lineplot(data=sales_long, x="Date", y="Sales", hue="Product", marker='o')
plt.title("Xu Hướng Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

## 3. Bar Plot

```python
# Dữ liệu
products = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"],
    "Sales": [450000000, 75000000, 160000000, 250000000, 120000000],
    "Category": ["Electronics", "Accessories", "Accessories", 
                 "Electronics", "Audio"]
})

# Bar plot
plt.figure(figsize=(10, 6))
sns.barplot(data=products, x="Product", y="Sales", hue="Category")
plt.title("Doanh Số Theo Sản Phẩm", fontsize=16, fontweight='bold')
plt.xlabel("Sản Phẩm", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

## 4. Box Plot

Hiển thị phân bố và outliers:

```python
# Tạo dữ liệu
np.random.seed(42)
sales_by_category = pd.DataFrame({
    "Category": np.repeat(["Electronics", "Accessories", "Audio"], 100),
    "Sales": np.concatenate([
        np.random.normal(50000000, 10000000, 100),
        np.random.normal(20000000, 5000000, 100),
        np.random.normal(15000000, 4000000, 100)
    ])
})

# Box plot
plt.figure(figsize=(10, 6))
sns.boxplot(data=sales_by_category, x="Category", y="Sales")
plt.title("Phân Bố Doanh Số Theo Category", fontsize=16, fontweight='bold')
plt.xlabel("Category", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: So sánh doanh số
```python
# Box plot với hue
sales_data = pd.DataFrame({
    "Product": np.repeat(["Laptop", "Mouse", "Keyboard"], 50),
    "Region": np.tile(["North", "South"], 75),
    "Sales": np.concatenate([
        np.random.normal(50000000, 10000000, 50),
        np.random.normal(20000000, 5000000, 50),
        np.random.normal(30000000, 8000000, 50)
    ])
})

plt.figure(figsize=(12, 6))
sns.boxplot(data=sales_data, x="Product", y="Sales", hue="Region")
plt.title("Phân Bố Doanh Số Theo Sản Phẩm và Khu Vực", fontsize=16, fontweight='bold')
plt.xlabel("Sản Phẩm", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.tight_layout()
plt.show()
```

## 5. Violin Plot

Kết hợp box plot và distribution:

```python
plt.figure(figsize=(10, 6))
sns.violinplot(data=sales_by_category, x="Category", y="Sales")
plt.title("Phân Bố Doanh Số (Violin Plot)", fontsize=16, fontweight='bold')
plt.xlabel("Category", fontsize=12)
plt.ylabel("Doanh Số (VND)", fontsize=12)
plt.tight_layout()
plt.show()
```

## 6. Heatmap

Hiển thị correlation hoặc matrix:

```python
# Correlation matrix
sales_corr = pd.DataFrame({
    "Laptop": np.random.randn(100),
    "Mouse": np.random.randn(100),
    "Keyboard": np.random.randn(100),
    "Monitor": np.random.randn(100)
})

correlation = sales_corr.corr()

plt.figure(figsize=(10, 8))
sns.heatmap(correlation, annot=True, cmap="coolwarm", center=0, 
            square=True, linewidths=1, cbar_kws={"shrink": 0.8})
plt.title("Correlation Matrix", fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: Heatmap doanh số
```python
# Doanh số theo sản phẩm và tháng
sales_matrix = pd.DataFrame({
    "Jan": [30, 150, 80, 50],
    "Feb": [35, 160, 85, 55],
    "Mar": [40, 170, 90, 60],
    "Apr": [45, 180, 95, 65]
}, index=["Laptop", "Mouse", "Keyboard", "Monitor"])

plt.figure(figsize=(10, 6))
sns.heatmap(sales_matrix, annot=True, fmt="d", cmap="YlOrRd", 
            linewidths=0.5, cbar_kws={"label": "Số Lượng Bán"})
plt.title("Doanh Số Theo Sản Phẩm và Tháng", fontsize=16, fontweight='bold')
plt.xlabel("Tháng", fontsize=12)
plt.ylabel("Sản Phẩm", fontsize=12)
plt.tight_layout()
plt.show()
```

## 7. Pair Plot

Xem mối quan hệ giữa nhiều biến:

```python
# Tạo dữ liệu
sales_analysis = pd.DataFrame({
    "Price": np.random.randint(500000, 20000000, 100),
    "Sales": np.random.randint(10, 200, 100),
    "Revenue": np.random.randint(10000000, 50000000, 100),
    "Category": np.random.choice(["Electronics", "Accessories", "Audio"], 100)
})

# Pair plot
sns.pairplot(sales_analysis, hue="Category", diag_kind="kde")
plt.suptitle("Pair Plot - Phân Tích Doanh Số", y=1.02, fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()
```

## 8. Distribution Plot

```python
# Histogram với KDE
plt.figure(figsize=(10, 6))
sns.histplot(data=sales_by_category, x="Sales", hue="Category", 
             kde=True, alpha=0.7)
plt.title("Phân Bố Doanh Số", fontsize=16, fontweight='bold')
plt.xlabel("Doanh Số (VND)", fontsize=12)
plt.ylabel("Tần Suất", fontsize=12)
plt.tight_layout()
plt.show()
```

## 9. Regression Plot

```python
# Scatter plot với regression line
sales_reg = pd.DataFrame({
    "Price": np.random.randint(500000, 20000000, 100),
    "Sales": np.random.randint(10, 200, 100)
})

plt.figure(figsize=(10, 6))
sns.regplot(data=sales_reg, x="Price", y="Sales", 
           scatter_kws={"alpha": 0.6}, line_kws={"color": "red"})
plt.title("Mối Quan Hệ Giữa Giá và Số Lượng Bán", fontsize=16, fontweight='bold')
plt.xlabel("Giá (VND)", fontsize=12)
plt.ylabel("Số Lượng Bán", fontsize=12)
plt.tight_layout()
plt.show()
```

## 10. Facet Grid

Tạo nhiều subplots theo categories:

```python
# Tạo dữ liệu
sales_facet = pd.DataFrame({
    "Date": pd.date_range("2024-01-01", periods=60, freq="D"),
    "Product": np.repeat(["Laptop", "Mouse", "Keyboard"], 20),
    "Sales": np.random.randint(10, 200, 60)
})

# Facet grid
g = sns.FacetGrid(sales_facet, col="Product", col_wrap=3, height=4)
g.map(sns.lineplot, "Date", "Sales", marker='o')
g.set_axis_labels("Ngày", "Số Lượng Bán")
g.set_titles("{col_name}")
plt.suptitle("Doanh Số Theo Sản Phẩm", y=1.02, fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()
```

## 11. Tùy Chỉnh Style

```python
# Set style
sns.set_style("whitegrid")
sns.set_palette("Set2")

# Hoặc custom palette
custom_palette = ["#2E86AB", "#A23B72", "#F18F01", "#C73E1D"]
sns.set_palette(custom_palette)

# Context (size)
sns.set_context("paper")  # nhỏ nhất
sns.set_context("notebook")  # mặc định
sns.set_context("talk")  # lớn hơn
sns.set_context("poster")  # lớn nhất
```

## Bài Tập Thực Hành

### Bài 1: Statistical Dashboard
```python
# Tạo dashboard với:
# 1. Box plot: Phân bố doanh số theo category
# 2. Heatmap: Correlation giữa các biến
# 3. Pair plot: Mối quan hệ giữa Price, Sales, Revenue
# 4. Distribution plot: Phân bố doanh số
```

### Bài 2: So Sánh Nhiều Nhóm
```python
# Tạo visualizations so sánh:
# - Doanh số theo region và product
# - Sử dụng violin plot và box plot
# - Facet grid để tách theo category
```

### Bài 3: Correlation Analysis
```python
# Phân tích correlation:
# - Tạo correlation matrix
# - Heatmap với annotations
# - Regression plots cho các cặp biến quan trọng
```

## Tổng Kết

✅ Đã học:
- Line plots và bar plots với Seaborn
- Box plots và violin plots
- Heatmaps
- Pair plots
- Distribution plots
- Regression plots
- Facet grids
- Tùy chỉnh style

**Tiếp theo**: [03. Plotly](03-plotly.md)

