# 03. Correlation & Regression

## Mục Tiêu
- Hiểu được correlation và regression, khi nào sử dụng
- Tính correlation coefficient và interpret kết quả
- Xây dựng và đánh giá regression models
- Áp dụng vào các tình huống thực tế: dự đoán, phân tích mối quan hệ

## Correlation (Tương Quan)

### Correlation Là Gì?

**Correlation (tương quan)** đo lường mối quan hệ tuyến tính giữa 2 biến.

**Đặc điểm**:
- **Range**: -1 đến +1
- **+1**: Tương quan dương hoàn hảo (tăng cùng nhau)
- **-1**: Tương quan âm hoàn hảo (một tăng, một giảm)
- **0**: Không có tương quan tuyến tính

**Lưu ý quan trọng**: **Correlation ≠ Causation**
- Correlation chỉ cho biết 2 biến có liên quan, không phải nguyên nhân
- Ví dụ: Ice cream sales và drowning deaths có correlation cao, nhưng không có nghĩa là ice cream gây chết đuối

### Pearson Correlation Coefficient

**Công thức**:
\[
r = \frac{\sum (x_i - \bar{x})(y_i - \bar{y})}{\sqrt{\sum (x_i - \bar{x})^2 \sum (y_i - \bar{y})^2}}
\]

**Interpretation**:
- **|r| > 0.7**: Tương quan mạnh
- **0.3 < |r| < 0.7**: Tương quan trung bình
- **|r| < 0.3**: Tương quan yếu

```python
import pandas as pd
import numpy as np
from scipy.stats import pearsonr
import matplotlib.pyplot as plt

# Dữ liệu: Giá quảng cáo vs Doanh số
np.random.seed(42)
ad_spend = np.random.normal(100, 20, 100)  # Triệu VND
sales = 50 + 0.8 * ad_spend + np.random.normal(0, 10, 100)  # Tỷ VND

df = pd.DataFrame({'ad_spend': ad_spend, 'sales': sales})

# Tính correlation
correlation, p_value = pearsonr(df['ad_spend'], df['sales'])

print(f"Correlation coefficient: {correlation:.3f}")
print(f"P-value: {p_value:.4f}")

if abs(correlation) > 0.7:
    strength = "mạnh"
elif abs(correlation) > 0.3:
    strength = "trung bình"
else:
    strength = "yếu"

direction = "dương" if correlation > 0 else "âm"
print(f"\n→ Tương quan {strength} {direction}")

# Visualize
plt.figure(figsize=(10, 6))
plt.scatter(df['ad_spend'], df['sales'], alpha=0.6)
plt.xlabel('Chi Phí Quảng Cáo (Triệu VND)')
plt.ylabel('Doanh Số (Tỷ VND)')
plt.title(f'Correlation: {correlation:.3f}')
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Correlation Matrix

Khi có nhiều biến, tính correlation matrix:

```python
# Dữ liệu với nhiều biến
np.random.seed(42)
data = {
    'ad_spend': np.random.normal(100, 20, 100),
    'sales': np.random.normal(130, 15, 100),
    'website_traffic': np.random.normal(5000, 1000, 100),
    'conversion_rate': np.random.normal(0.03, 0.01, 100)
}

df = pd.DataFrame(data)
# Tạo correlation với sales
df['sales'] = 50 + 0.8 * df['ad_spend'] + 0.01 * df['website_traffic'] + np.random.normal(0, 5, 100)

# Correlation matrix
corr_matrix = df.corr()
print("=== CORRELATION MATRIX ===")
print(corr_matrix.round(3))

# Visualize correlation matrix
import seaborn as sns
plt.figure(figsize=(10, 8))
sns.heatmap(corr_matrix, annot=True, fmt='.3f', cmap='coolwarm', center=0,
            square=True, linewidths=1, cbar_kws={"shrink": 0.8})
plt.title('Correlation Matrix')
plt.tight_layout()
plt.show()
```

## Regression (Hồi Quy)

### Regression Là Gì?

**Regression (hồi quy)** dùng để:
- **Dự đoán**: Dự đoán giá trị của biến phụ thuộc (Y) dựa trên biến độc lập (X)
- **Hiểu mối quan hệ**: Xem X ảnh hưởng Y như thế nào

**Khác với Correlation**:
- Correlation: Chỉ đo lường mối quan hệ
- Regression: Xây dựng model để dự đoán và hiểu impact

### Simple Linear Regression

**Mô hình**: \(y = \beta_0 + \beta_1 x + \epsilon\)

- **β₀ (intercept)**: Giá trị Y khi X = 0
- **β₁ (slope)**: Thay đổi Y khi X tăng 1 đơn vị
- **ε (error)**: Phần dư, không giải thích được

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error
import numpy as np
import matplotlib.pyplot as plt

# Dữ liệu: Price vs Sales
np.random.seed(42)
price = np.random.randint(500000, 20000000, 100)  # VND
sales = 200 - (price / 100000) + np.random.normal(0, 10, 100)  # Units

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

print("=== LINEAR REGRESSION RESULTS ===")
print(f"Intercept (β₀): {model.intercept_:.2f}")
print(f"Slope (β₁): {model.coef_[0]:.6f}")
print(f"R²: {r2:.3f}")
print(f"RMSE: {rmse:.2f}")

# Interpretation
print(f"\n=== INTERPRETATION ===")
print(f"Khi giá tăng 1 triệu VND, sales giảm {abs(model.coef_[0] * 1000000):.0f} units")
print(f"R² = {r2:.3f} → Model giải thích {r2*100:.1f}% variance")

# Visualize
plt.figure(figsize=(10, 6))
plt.scatter(price, y, alpha=0.6, label='Actual')
plt.plot(price, y_pred, color='red', linewidth=2, label='Predicted')
plt.xlabel('Price (VND)')
plt.ylabel('Sales (Units)')
plt.title(f'Linear Regression (R² = {r2:.3f})')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Multiple Linear Regression

Khi có nhiều biến độc lập:

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error
import pandas as pd
import numpy as np

# Dữ liệu: Ad spend, Price, Traffic → Sales
np.random.seed(42)
n = 100
ad_spend = np.random.normal(100, 20, n)
price = np.random.normal(1000000, 200000, n)
traffic = np.random.normal(5000, 1000, n)

# Sales phụ thuộc vào cả 3 biến
sales = (50 + 0.5 * ad_spend - 0.00001 * price + 0.005 * traffic + 
         np.random.normal(0, 5, n))

df = pd.DataFrame({
    'ad_spend': ad_spend,
    'price': price,
    'traffic': traffic,
    'sales': sales
})

# Prepare data
X = df[['ad_spend', 'price', 'traffic']]
y = df['sales']

# Train model
model = LinearRegression()
model.fit(X, y)

# Predictions
y_pred = model.predict(X)

# Metrics
r2 = r2_score(y, y_pred)
rmse = np.sqrt(mean_squared_error(y, y_pred))

print("=== MULTIPLE LINEAR REGRESSION ===")
print(f"R²: {r2:.3f}")
print(f"RMSE: {rmse:.2f}")

# Coefficients
coefficients = pd.DataFrame({
    'feature': X.columns,
    'coefficient': model.coef_,
    'abs_coefficient': np.abs(model.coef_)
}).sort_values('abs_coefficient', ascending=False)

print("\n=== COEFFICIENTS ===")
print(coefficients)

# Interpretation
print("\n=== INTERPRETATION ===")
for idx, row in coefficients.iterrows():
    feature = row['feature']
    coef = row['coefficient']
    if feature == 'ad_spend':
        print(f"{feature}: Tăng 1 triệu VND → Sales tăng {coef:.2f} tỷ VND")
    elif feature == 'price':
        print(f"{feature}: Tăng 1 VND → Sales thay đổi {coef:.8f} tỷ VND")
    elif feature == 'traffic':
        print(f"{feature}: Tăng 1 visitor → Sales tăng {coef:.4f} tỷ VND")
```

### Model Evaluation

#### R² (R-Squared)

**R²** đo lường tỷ lệ variance được giải thích bởi model:
- **R² = 1**: Model hoàn hảo
- **R² = 0**: Model không tốt hơn mean
- **R² < 0**: Model tệ hơn mean

**Lưu ý**: R² cao không có nghĩa là model tốt (có thể overfitting)

#### RMSE (Root Mean Squared Error)

**RMSE** đo lường sai số trung bình:
- RMSE càng thấp càng tốt
- Cùng đơn vị với biến phụ thuộc

#### Residuals Analysis

Phân tích phần dư để kiểm tra assumptions:

```python
import matplotlib.pyplot as plt
import numpy as np
from scipy import stats

# Tính residuals
residuals = y - y_pred

# Residuals plot
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Residuals vs Predicted
axes[0].scatter(y_pred, residuals, alpha=0.6)
axes[0].axhline(y=0, color='red', linestyle='--')
axes[0].set_xlabel('Predicted Values')
axes[0].set_ylabel('Residuals')
axes[0].set_title('Residuals vs Predicted')
axes[0].grid(True, alpha=0.3)

# Q-Q plot (kiểm tra normal distribution)
stats.probplot(residuals, dist="norm", plot=axes[1])
axes[1].set_title('Q-Q Plot (Normal Distribution Check)')
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# Kiểm tra assumptions
print("=== RESIDUAL ANALYSIS ===")
print(f"Mean of residuals: {residuals.mean():.4f} (should be ~0)")
print(f"Std of residuals: {residuals.std():.4f}")

# Test normality
from scipy.stats import shapiro
stat, p_value = shapiro(residuals)
print(f"Shapiro-Wilk test (normality): p-value = {p_value:.4f}")
if p_value > 0.05:
    print("→ Residuals có phân bố normal (OK)")
else:
    print("→ Residuals không có phân bố normal (cần xem xét)")
```

### Polynomial Regression

Khi mối quan hệ không tuyến tính:

```python
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score
import numpy as np
import matplotlib.pyplot as plt

# Dữ liệu với mối quan hệ phi tuyến
np.random.seed(42)
X = np.linspace(0, 10, 100)
y = 2 + 3*X - 0.5*X**2 + np.random.normal(0, 2, 100)

# Linear regression
X_linear = X.reshape(-1, 1)
model_linear = LinearRegression()
model_linear.fit(X_linear, y)
y_pred_linear = model_linear.predict(X_linear)
r2_linear = r2_score(y, y_pred_linear)

# Polynomial regression (degree 2)
poly_features = PolynomialFeatures(degree=2)
X_poly = poly_features.fit_transform(X_linear)
model_poly = LinearRegression()
model_poly.fit(X_poly, y)
y_pred_poly = model_poly.predict(X_poly)
r2_poly = r2_score(y, y_pred_poly)

print("=== POLYNOMIAL REGRESSION ===")
print(f"Linear R²: {r2_linear:.3f}")
print(f"Polynomial R²: {r2_poly:.3f}")

# Visualize
plt.figure(figsize=(12, 5))
plt.scatter(X, y, alpha=0.6, label='Actual')
plt.plot(X, y_pred_linear, color='red', linewidth=2, label=f'Linear (R²={r2_linear:.3f})')
plt.plot(X, y_pred_poly, color='green', linewidth=2, label=f'Polynomial (R²={r2_poly:.3f})')
plt.xlabel('X')
plt.ylabel('Y')
plt.title('Linear vs Polynomial Regression')
plt.legend()
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## Business Applications

### Case Study: Dự Đoán Doanh Số

```python
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error
from sklearn.model_selection import train_test_split

# Dữ liệu: Marketing spend → Sales
np.random.seed(42)
data = {
    'month': range(1, 13),
    'ad_spend': np.random.normal(100, 20, 12),
    'email_campaigns': np.random.randint(5, 15, 12),
    'social_media_posts': np.random.randint(20, 40, 12),
    'sales': np.random.normal(150, 20, 12)
}

# Tạo relationship
df = pd.DataFrame(data)
df['sales'] = (50 + 0.8 * df['ad_spend'] + 2 * df['email_campaigns'] + 
               0.5 * df['social_media_posts'] + np.random.normal(0, 5, 12))

# Prepare data
X = df[['ad_spend', 'email_campaigns', 'social_media_posts']]
y = df['sales']

# Train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

# Train model
model = LinearRegression()
model.fit(X_train, y_train)

# Predictions
y_train_pred = model.predict(X_train)
y_test_pred = model.predict(X_test)

# Metrics
train_r2 = r2_score(y_train, y_train_pred)
test_r2 = r2_score(y_test, y_test_pred)
test_rmse = np.sqrt(mean_squared_error(y_test, y_test_pred))

print("=== SALES PREDICTION MODEL ===")
print(f"Train R²: {train_r2:.3f}")
print(f"Test R²: {test_r2:.3f}")
print(f"Test RMSE: {test_rmse:.2f}")

# Coefficients
coefficients = pd.DataFrame({
    'feature': X.columns,
    'coefficient': model.coef_
})
print("\n=== COEFFICIENTS ===")
print(coefficients)

# Dự đoán cho tháng tới
next_month = pd.DataFrame({
    'ad_spend': [120],
    'email_campaigns': [10],
    'social_media_posts': [30]
})
predicted_sales = model.predict(next_month)
print(f"\n=== PREDICTION ===")
print(f"Dự đoán doanh số tháng tới: {predicted_sales[0]:.2f} tỷ VND")
```

## Best Practices

1. **Kiểm tra assumptions**: Linear relationship, normal residuals, homoscedasticity
2. **Feature selection**: Chọn features quan trọng, tránh multicollinearity
3. **Train-test split**: Đánh giá model trên test set, không chỉ train set
4. **Interpret coefficients**: Hiểu ý nghĩa business của coefficients
5. **Consider non-linear**: Nếu linear không tốt, thử polynomial hoặc other models

## 📝 Bài Tập Thực Hành

### Bài Tập 1: Correlation Analysis
Phân tích correlation giữa các biến marketing và sales.

### Bài Tập 2: Sales Prediction
Xây dựng regression model để dự đoán sales dựa trên marketing spend.

### Bài Tập 3: Model Evaluation
Đánh giá model và phân tích residuals để cải thiện.

---

**Lưu ý**: Regression là công cụ mạnh mẽ, nhưng cần hiểu assumptions và limitations. Luôn validate model và interpret kết quả trong business context.

