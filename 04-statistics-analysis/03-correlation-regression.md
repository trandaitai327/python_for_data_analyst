# 03. Correlation & Regression

## Mục Tiêu
- Hiểu về correlation và regression
- Tính correlation coefficient
- Thực hiện linear regression
- Đánh giá model quality
- Dự đoán với regression model

## Correlation (Tương Quan)

Correlation đo lường mối quan hệ tuyến tính giữa 2 biến.

### Pearson Correlation

```python
import pandas as pd
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
import seaborn as sns

# Tạo dữ liệu có correlation
np.random.seed(42)
x = np.random.normal(10000000, 2000000, 100)
y = x * 0.5 + np.random.normal(0, 500000, 100)  # y tương quan với x

# Pearson correlation
correlation, p_value = stats.pearsonr(x, y)

print(f"Correlation coefficient: {correlation:.3f}")
print(f"P-value: {p_value:.4f}")

if abs(correlation) > 0.7:
    strength = "mạnh"
elif abs(correlation) > 0.4:
    strength = "trung bình"
else:
    strength = "yếu"

if correlation > 0:
    direction = "dương"
else:
    direction = "âm"

print(f"→ Mối quan hệ {strength} và {direction}")
```

### Correlation Matrix

```python
# Tạo dữ liệu nhiều biến
data = pd.DataFrame({
    "Price": np.random.normal(10000000, 2000000, 100),
    "Sales": np.random.normal(50, 10, 100),
    "Revenue": np.random.normal(500000000, 100000000, 100),
    "Marketing": np.random.normal(50000000, 10000000, 100)
})

# Tính correlation
correlation_matrix = data.corr()
print("Correlation Matrix:")
print(correlation_matrix)

# Visualization
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap="coolwarm", center=0,
            square=True, linewidths=1, fmt='.2f')
plt.title("Correlation Matrix", fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()
```

**Ví dụ thực tế**: Phân tích correlation
```python
# Dữ liệu bán hàng
sales_data = pd.DataFrame({
    "Price": np.random.randint(500000, 20000000, 100),
    "Sales": np.random.randint(10, 200, 100),
    "Marketing_Spend": np.random.randint(10000000, 100000000, 100),
    "Customer_Satisfaction": np.random.uniform(3.0, 5.0, 100)
})

# Tính revenue
sales_data["Revenue"] = sales_data["Price"] * sales_data["Sales"]

# Correlation analysis
corr = sales_data[["Price", "Sales", "Marketing_Spend", 
                    "Customer_Satisfaction", "Revenue"]].corr()

print("Correlation với Revenue:")
print(corr["Revenue"].sort_values(ascending=False))
```

## Linear Regression (Hồi Quy Tuyến Tính)

### Simple Linear Regression

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error

# Dữ liệu: Price vs Sales
np.random.seed(42)
price = np.random.randint(500000, 20000000, 100)
sales = 200 - (price / 100000) + np.random.normal(0, 10, 100)

# Reshape cho sklearn
X = price.reshape(-1, 1)
y = sales

# Train model
model = LinearRegression()
model.fit(X, y)

# Predictions
y_pred = model.predict(X)

# Metrics
r2 = r2_score(y, y_pred)
rmse = np.sqrt(mean_squared_error(y, y_pred))

print("Simple Linear Regression:")
print(f"Intercept: {model.intercept_:.2f}")
print(f"Coefficient: {model.coef_[0]:.6f}")
print(f"R²: {r2:.3f}")
print(f"RMSE: {rmse:.2f}")

# Visualization
plt.figure(figsize=(10, 6))
plt.scatter(price, sales, alpha=0.6, label='Data')
plt.plot(price, y_pred, color='red', linewidth=2, label='Regression Line')
plt.xlabel("Price (VND)", fontsize=12)
plt.ylabel("Sales", fontsize=12)
plt.title("Price vs Sales", fontsize=16, fontweight='bold')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Multiple Linear Regression

```python
# Dữ liệu: Nhiều features
np.random.seed(42)
data = pd.DataFrame({
    "Price": np.random.randint(500000, 20000000, 100),
    "Marketing": np.random.randint(10000000, 100000000, 100),
    "Customer_Satisfaction": np.random.uniform(3.0, 5.0, 100),
    "Sales": np.random.randint(10, 200, 100)
})

# Target: Revenue
data["Revenue"] = (data["Price"] * data["Sales"] + 
                  data["Marketing"] * 0.1 + 
                  data["Customer_Satisfaction"] * 10000000 +
                  np.random.normal(0, 10000000, 100))

# Features và target
X = data[["Price", "Marketing", "Customer_Satisfaction", "Sales"]]
y = data["Revenue"]

# Train model
model = LinearRegression()
model.fit(X, y)

# Predictions
y_pred = model.predict(X)

# Metrics
r2 = r2_score(y, y_pred)
rmse = np.sqrt(mean_squared_error(y, y_pred))

print("Multiple Linear Regression:")
print(f"Intercept: {model.intercept_:,.0f}")
print("\nCoefficients:")
for feature, coef in zip(X.columns, model.coef_):
    print(f"  {feature}: {coef:,.2f}")
print(f"\nR²: {r2:.3f}")
print(f"RMSE: {rmse:,.0f} VND")
```

**Ví dụ thực tế**: Dự đoán doanh số
```python
# Dữ liệu lịch sử
historical_data = pd.DataFrame({
    "Month": range(1, 13),
    "Marketing_Spend": np.random.randint(50000000, 100000000, 12),
    "Price": np.random.randint(10000000, 15000000, 12),
    "Sales": np.random.randint(30, 60, 12)
})

historical_data["Revenue"] = (historical_data["Price"] * historical_data["Sales"] +
                              historical_data["Marketing_Spend"] * 0.2 +
                              np.random.normal(0, 5000000, 12))

# Train model
X = historical_data[["Marketing_Spend", "Price", "Sales"]]
y = historical_data["Revenue"]

model = LinearRegression()
model.fit(X, y)

# Dự đoán tháng tiếp theo
next_month = pd.DataFrame({
    "Marketing_Spend": [80000000],
    "Price": [12000000],
    "Sales": [50]
})

predicted_revenue = model.predict(next_month)
print(f"Dự đoán doanh số tháng tiếp theo: {predicted_revenue[0]:,.0f} VND")
```

## Model Evaluation (Đánh Giá Model)

### R² Score

```python
# R² đo lường % variance được giải thích bởi model
# R² = 1: Perfect fit
# R² = 0: Model không tốt hơn mean
# R² < 0: Model tệ hơn mean

r2 = r2_score(y, y_pred)
print(f"R² Score: {r2:.3f}")
print(f"→ Model giải thích {r2*100:.1f}% variance")
```

### Residuals Analysis

```python
# Residuals = y_actual - y_predicted
residuals = y - y_pred

# Plot residuals
plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)
plt.scatter(y_pred, residuals, alpha=0.6)
plt.axhline(y=0, color='red', linestyle='--')
plt.xlabel("Predicted", fontsize=12)
plt.ylabel("Residuals", fontsize=12)
plt.title("Residuals Plot", fontsize=14, fontweight='bold')
plt.grid(True, alpha=0.3)

plt.subplot(1, 2, 2)
plt.hist(residuals, bins=20, alpha=0.7, edgecolor='black')
plt.xlabel("Residuals", fontsize=12)
plt.ylabel("Frequency", fontsize=12)
plt.title("Residuals Distribution", fontsize=14, fontweight='bold')
plt.grid(True, alpha=0.3, axis='y')

plt.tight_layout()
plt.show()

# Kiểm tra normality của residuals
from scipy.stats import shapiro
stat, p_value = shapiro(residuals)
print(f"Shapiro-Wilk test p-value: {p_value:.4f}")
if p_value > 0.05:
    print("→ Residuals phân bố chuẩn (tốt)")
else:
    print("→ Residuals không phân bố chuẩn")
```

## Polynomial Regression

Khi mối quan hệ không tuyến tính:

```python
from sklearn.preprocessing import PolynomialFeatures

# Tạo dữ liệu không tuyến tính
x = np.linspace(0, 20, 100)
y = 2 * x**2 - 3 * x + 5 + np.random.normal(0, 10, 100)

# Polynomial features
poly_features = PolynomialFeatures(degree=2)
X_poly = poly_features.fit_transform(x.reshape(-1, 1))

# Train model
model = LinearRegression()
model.fit(X_poly, y)

# Predictions
y_pred = model.predict(X_poly)

# Metrics
r2 = r2_score(y, y_pred)
print(f"Polynomial Regression R²: {r2:.3f}")

# Visualization
plt.figure(figsize=(10, 6))
plt.scatter(x, y, alpha=0.6, label='Data')
plt.plot(x, y_pred, color='red', linewidth=2, label='Polynomial Fit')
plt.xlabel("X", fontsize=12)
plt.ylabel("Y", fontsize=12)
plt.title("Polynomial Regression", fontsize=16, fontweight='bold')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## Case Study: Phân Tích Doanh Số

```python
# Tạo dữ liệu thực tế
np.random.seed(42)
sales_analysis = pd.DataFrame({
    "Price": np.random.randint(5000000, 20000000, 200),
    "Marketing_Spend": np.random.randint(20000000, 100000000, 200),
    "Customer_Satisfaction": np.random.uniform(3.5, 5.0, 200),
    "Competitor_Price": np.random.randint(4500000, 19000000, 200),
    "Season": np.random.choice(["Spring", "Summer", "Fall", "Winter"], 200)
})

# Tính Sales (có mối quan hệ với các biến)
sales_analysis["Sales"] = (
    100 - (sales_analysis["Price"] / 200000) +
    (sales_analysis["Marketing_Spend"] / 1000000) +
    (sales_analysis["Customer_Satisfaction"] * 10) -
    ((sales_analysis["Competitor_Price"] - sales_analysis["Price"]) / 100000) +
    np.random.normal(0, 5, 200)
)

# 1. Correlation analysis
print("CORRELATION ANALYSIS")
print("=" * 50)
corr_with_sales = sales_analysis[["Price", "Marketing_Spend", 
                                   "Customer_Satisfaction", "Competitor_Price", 
                                   "Sales"]].corr()["Sales"].sort_values(ascending=False)
print(corr_with_sales)

# 2. Linear regression
print("\nLINEAR REGRESSION")
print("=" * 50)
X = sales_analysis[["Price", "Marketing_Spend", "Customer_Satisfaction", "Competitor_Price"]]
y = sales_analysis["Sales"]

model = LinearRegression()
model.fit(X, y)
y_pred = model.predict(X)

print(f"R²: {r2_score(y, y_pred):.3f}")
print(f"RMSE: {np.sqrt(mean_squared_error(y, y_pred)):.2f}")
print("\nCoefficients:")
for feature, coef in zip(X.columns, model.coef_):
    print(f"  {feature}: {coef:.4f}")

# 3. Predictions
new_data = pd.DataFrame({
    "Price": [12000000],
    "Marketing_Spend": [60000000],
    "Customer_Satisfaction": [4.5],
    "Competitor_Price": [11500000]
})

predicted_sales = model.predict(new_data)
print(f"\nDự đoán Sales: {predicted_sales[0]:.1f}")
```

## Bài Tập Thực Hành

### Bài 1: Correlation Analysis
```python
# Phân tích correlation giữa:
# - Price và Sales
# - Marketing và Revenue
# - Customer Satisfaction và Sales
# Tạo correlation matrix và heatmap
```

### Bài 2: Linear Regression
```python
# Tạo model dự đoán Revenue dựa trên:
# - Price
# - Marketing Spend
# - Sales
# Đánh giá model với R² và RMSE
```

### Bài 3: Model Evaluation
```python
# Đánh giá model:
# - Tính R²
# - Vẽ residuals plot
# - Kiểm tra normality của residuals
# - Dự đoán cho dữ liệu mới
```

## Tổng Kết

✅ Đã học:
- Correlation (Pearson)
- Correlation matrix
- Simple và multiple linear regression
- Model evaluation (R², RMSE)
- Residuals analysis
- Polynomial regression

**Tiếp theo**: [Phần 5: Nâng Cao](05-advanced/01-api-web-scraping.md)

