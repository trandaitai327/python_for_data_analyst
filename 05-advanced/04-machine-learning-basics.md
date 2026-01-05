# 04. Machine Learning Basics

## Mục Tiêu
- Hiểu về machine learning cơ bản
- Sử dụng scikit-learn
- Xây dựng và đánh giá models
- Áp dụng vào phân tích dữ liệu

## Machine Learning Cho Data Analyst

ML giúp Data Analyst:
- **Dự đoán**: Dự đoán doanh số, churn, etc.
- **Phân loại**: Phân loại khách hàng, sản phẩm
- **Clustering**: Nhóm khách hàng tương tự
- **Recommendation**: Gợi ý sản phẩm

## 1. Scikit-Learn Cơ Bản

### Cài Đặt

```bash
pip install scikit-learn
```

### Workflow Cơ Bản

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
import pandas as pd
import numpy as np

# 1. Load và prepare data
# 2. Split data (train/test)
# 3. Train model
# 4. Evaluate model
# 5. Make predictions
```

## 2. Linear Regression (Đã Học ở Phần 4)

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score, mean_squared_error

# Dữ liệu
np.random.seed(42)
X = np.random.randn(100, 1) * 10000000 + 10000000
y = X.flatten() * 0.5 + np.random.randn(100) * 1000000

# Split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train
model = LinearRegression()
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)

# Evaluate
r2 = r2_score(y_test, y_pred)
rmse = np.sqrt(mean_squared_error(y_test, y_pred))

print(f"R²: {r2:.3f}")
print(f"RMSE: {rmse:,.0f}")
```

## 3. Classification (Phân Loại)

### Logistic Regression

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix

# Tạo dữ liệu phân loại
np.random.seed(42)
# Feature: spending amount
X = np.random.randn(200, 1) * 1000000 + 5000000
# Target: churn (0 = no, 1 = yes)
y = (X.flatten() > 5000000).astype(int)

# Split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train
model = LogisticRegression()
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)

# Evaluate
accuracy = accuracy_score(y_test, y_pred)
print(f"Accuracy: {accuracy:.3f}")
print("\nClassification Report:")
print(classification_report(y_test, y_pred))
print("\nConfusion Matrix:")
print(confusion_matrix(y_test, y_pred))
```

**Ví dụ thực tế**: Dự đoán churn
```python
# Dữ liệu khách hàng
np.random.seed(42)
customer_data = pd.DataFrame({
    "Spending": np.random.normal(5000000, 2000000, 1000),
    "Frequency": np.random.poisson(10, 1000),
    "Last_Purchase_Days": np.random.exponential(30, 1000)
})

# Target: Churn (1 nếu churn, 0 nếu không)
customer_data["Churn"] = (
    (customer_data["Spending"] < 3000000) |
    (customer_data["Last_Purchase_Days"] > 60)
).astype(int)

# Features
X = customer_data[["Spending", "Frequency", "Last_Purchase_Days"]]
y = customer_data["Churn"]

# Split và train
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

model = LogisticRegression()
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)

print(f"Churn Prediction Accuracy: {accuracy:.3f}")
print("\nFeature Importance:")
for feature, coef in zip(X.columns, model.coef_[0]):
    print(f"  {feature}: {coef:.4f}")
```

## 4. Decision Tree

```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.tree import plot_tree
import matplotlib.pyplot as plt

# Dữ liệu
X = customer_data[["Spending", "Frequency", "Last_Purchase_Days"]]
y = customer_data["Churn"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Train
model = DecisionTreeClassifier(max_depth=3, random_state=42)
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)

print(f"Decision Tree Accuracy: {accuracy:.3f}")

# Feature importance
feature_importance = pd.DataFrame({
    "Feature": X.columns,
    "Importance": model.feature_importances_
}).sort_values("Importance", ascending=False)

print("\nFeature Importance:")
print(feature_importance)
```

## 5. Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

# Train
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# Predict
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)

print(f"Random Forest Accuracy: {accuracy:.3f}")

# Feature importance
feature_importance = pd.DataFrame({
    "Feature": X.columns,
    "Importance": model.feature_importances_
}).sort_values("Importance", ascending=False)

print("\nFeature Importance:")
print(feature_importance)
```

## 6. Clustering (K-Means)

```python
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Dữ liệu khách hàng
customer_features = customer_data[["Spending", "Frequency", "Last_Purchase_Days"]]

# Scale data
scaler = StandardScaler()
X_scaled = scaler.fit_transform(customer_features)

# K-Means clustering
kmeans = KMeans(n_clusters=3, random_state=42)
clusters = kmeans.fit_predict(X_scaled)

# Thêm clusters vào data
customer_data["Cluster"] = clusters

# Phân tích clusters
cluster_summary = customer_data.groupby("Cluster").agg({
    "Spending": ["mean", "std"],
    "Frequency": ["mean", "std"],
    "Last_Purchase_Days": ["mean", "std"]
})

print("Cluster Summary:")
print(cluster_summary)
```

**Ví dụ thực tế**: Phân khúc khách hàng
```python
# Phân khúc khách hàng dựa trên hành vi
features = customer_data[["Spending", "Frequency"]]
X_scaled = scaler.fit_transform(features)

# Tìm số clusters tối ưu (Elbow method)
inertias = []
K_range = range(1, 11)

for k in K_range:
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(X_scaled)
    inertias.append(kmeans.inertia_)

# Visualize
plt.figure(figsize=(10, 6))
plt.plot(K_range, inertias, marker='o')
plt.xlabel("Number of Clusters (K)")
plt.ylabel("Inertia")
plt.title("Elbow Method")
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()

# Chọn K=3 và cluster
kmeans = KMeans(n_clusters=3, random_state=42)
customer_data["Segment"] = kmeans.fit_predict(X_scaled)

# Phân tích segments
segment_summary = customer_data.groupby("Segment").agg({
    "Spending": "mean",
    "Frequency": "mean",
    "Churn": "mean"
})

print("Customer Segments:")
print(segment_summary)
```

## 7. Model Evaluation

### Cross-Validation

```python
from sklearn.model_selection import cross_val_score

# Cross-validation
scores = cross_val_score(model, X_train, y_train, cv=5, scoring='accuracy')
print(f"Cross-validation scores: {scores}")
print(f"Mean accuracy: {scores.mean():.3f} (+/- {scores.std() * 2:.3f})")
```

### ROC Curve (Cho Classification)

```python
from sklearn.metrics import roc_curve, auc
from sklearn.metrics import roc_auc_score

# Predict probabilities
y_pred_proba = model.predict_proba(X_test)[:, 1]

# ROC curve
fpr, tpr, thresholds = roc_curve(y_test, y_pred_proba)
roc_auc = auc(fpr, tpr)

# Plot
plt.figure(figsize=(10, 6))
plt.plot(fpr, tpr, color='darkorange', lw=2, 
         label=f'ROC curve (AUC = {roc_auc:.2f})')
plt.plot([0, 1], [0, 1], color='navy', lw=2, linestyle='--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## 8. Feature Engineering

### Encoding Categorical Variables

```python
from sklearn.preprocessing import LabelEncoder, OneHotEncoder

# Label encoding
le = LabelEncoder()
customer_data["Region_Encoded"] = le.fit_transform(customer_data["Region"])

# One-hot encoding
from sklearn.preprocessing import OneHotEncoder
ohe = OneHotEncoder(sparse=False)
region_encoded = ohe.fit_transform(customer_data[["Region"]])
```

### Feature Scaling

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler

# Standard scaling
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Min-Max scaling
minmax_scaler = MinMaxScaler()
X_minmax = minmax_scaler.fit_transform(X)
```

## 9. Case Study: Dự Đoán Doanh Số

```python
# Tạo dữ liệu
np.random.seed(42)
sales_data = pd.DataFrame({
    "Price": np.random.randint(5000000, 20000000, 500),
    "Marketing_Spend": np.random.randint(20000000, 100000000, 500),
    "Customer_Satisfaction": np.random.uniform(3.0, 5.0, 500),
    "Season": np.random.choice(["Spring", "Summer", "Fall", "Winter"], 500)
})

# Target: Sales
sales_data["Sales"] = (
    sales_data["Price"] * 0.001 +
    sales_data["Marketing_Spend"] * 0.0001 +
    sales_data["Customer_Satisfaction"] * 10 +
    np.random.normal(0, 5, 500)
)

# Encode categorical
le = LabelEncoder()
sales_data["Season_Encoded"] = le.fit_transform(sales_data["Season"])

# Features
X = sales_data[["Price", "Marketing_Spend", "Customer_Satisfaction", "Season_Encoded"]]
y = sales_data["Sales"]

# Split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# Scale
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Train multiple models
from sklearn.ensemble import RandomForestRegressor
from sklearn.linear_model import LinearRegression

models = {
    "Linear Regression": LinearRegression(),
    "Random Forest": RandomForestRegressor(n_estimators=100, random_state=42)
}

results = {}
for name, model in models.items():
    model.fit(X_train_scaled, y_train)
    y_pred = model.predict(X_test_scaled)
    
    r2 = r2_score(y_test, y_pred)
    rmse = np.sqrt(mean_squared_error(y_test, y_pred))
    
    results[name] = {"R²": r2, "RMSE": rmse}
    
    print(f"\n{name}:")
    print(f"  R²: {r2:.3f}")
    print(f"  RMSE: {rmse:.2f}")

# So sánh models
results_df = pd.DataFrame(results).T
print("\nModel Comparison:")
print(results_df)
```

## Bài Tập Thực Hành

### Bài 1: Classification Model
```python
# Tạo model phân loại khách hàng:
# - Features: Spending, Frequency, Last_Purchase_Days
# - Target: Churn (0/1)
# - Sử dụng Logistic Regression và Random Forest
# - So sánh accuracy
```

### Bài 2: Clustering
```python
# Phân khúc khách hàng:
# - Sử dụng K-Means
# - Tìm số clusters tối ưu
# - Phân tích đặc điểm từng cluster
```

### Bài 3: Regression Model
```python
# Dự đoán doanh số:
# - Features: Price, Marketing, Season
# - Target: Sales
# - Sử dụng Linear Regression và Random Forest
# - Đánh giá với R² và RMSE
```

## Tổng Kết

✅ Đã học:
- Scikit-learn basics
- Linear và Logistic Regression
- Decision Tree và Random Forest
- K-Means Clustering
- Model evaluation
- Feature engineering
- Cross-validation

**Hoàn thành tài liệu! 🎉**

Bạn đã học xong toàn bộ tài liệu Python cho Data Analyst từ cơ bản đến nâng cao!

