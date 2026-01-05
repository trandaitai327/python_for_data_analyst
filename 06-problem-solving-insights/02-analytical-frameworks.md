# Các Framework Phân Tích Cho Data Analyst

## 🎯 Mục Tiêu

Sau bài học này, bạn sẽ:
- Nắm vững các framework phân tích phổ biến trong Data Analysis
- Biết cách áp dụng từng framework vào các tình huống cụ thể
- Có thể kết hợp nhiều framework để phân tích toàn diện

## 📚 Tổng Quan

Framework phân tích giúp bạn:
- **Có cấu trúc rõ ràng**: Không bỏ sót các khía cạnh quan trọng
- **Tiết kiệm thời gian**: Có sẵn template và checklist
- **Giao tiếp hiệu quả**: Dễ trình bày và thuyết phục stakeholders

## 🔍 Framework 1: AARRR (Pirate Metrics)

**Mục đích**: Phân tích user journey và product growth

### Các Giai Đoạn

1. **Acquisition** (Thu hút): Làm thế nào users tìm thấy bạn?
2. **Activation** (Kích hoạt): Users có trải nghiệm tốt lần đầu?
3. **Retention** (Giữ chân): Users có quay lại?
4. **Revenue** (Doanh thu): Users có trả tiền?
5. **Referral** (Giới thiệu): Users có giới thiệu người khác?

### Ví Dụ Thực Tế: Phân Tích App E-commerce

```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# Dữ liệu user journey
data = {
    'stage': ['Acquisition', 'Activation', 'Retention', 'Revenue', 'Referral'],
    'users': [10000, 6000, 3000, 1500, 500],
    'conversion_rate': [100, 60, 50, 50, 33.3]
}

df = pd.DataFrame(data)

# Tính funnel
df['drop_off'] = df['users'].diff().fillna(0) * -1
df['drop_off_pct'] = (df['drop_off'] / df['users'].shift(1) * 100).fillna(0)

print(df)

# Visualize funnel
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(14, 6))

# Funnel chart
ax1.barh(df['stage'], df['users'], color='steelblue')
ax1.set_xlabel('Số Lượng Users')
ax1.set_title('AARRR Funnel')

# Conversion rate
ax2.plot(df['stage'], df['conversion_rate'], marker='o', linewidth=2, markersize=8)
ax2.set_ylabel('Conversion Rate (%)')
ax2.set_title('Conversion Rate by Stage')
ax2.grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# Phân tích điểm nghẽn
bottleneck = df.loc[df['drop_off_pct'].idxmax()]
print(f"\nĐiểm nghẽn lớn nhất: {bottleneck['stage']}")
print(f"Tỷ lệ drop-off: {bottleneck['drop_off_pct']:.1f}%")
```

**Actionable Insights**:
- **Acquisition → Activation**: 40% users không kích hoạt → Cải thiện onboarding
- **Activation → Retention**: 50% users không quay lại → Tăng engagement
- **Retention → Revenue**: 50% users không mua → Tối ưu pricing/UX

## 🔍 Framework 2: SWOT Analysis (Dữ Liệu)

**Mục đích**: Phân tích điểm mạnh/yếu, cơ hội/thách thức dựa trên dữ liệu

### Cấu Trúc

```
        INTERNAL                    EXTERNAL
    ┌─────────────┬─────────────┐
    │             │             │
    │  STRENGTHS  │ OPPORTUNITIES│
    │             │             │
    ├─────────────┼─────────────┤
    │             │             │
    │  WEAKNESSES │  THREATS    │
    │             │             │
    └─────────────┴─────────────┘
```

### Ví Dụ: Phân Tích Competitor

```python
import pandas as pd

# Dữ liệu so sánh với competitors
competitor_data = {
    'metric': ['Market Share', 'User Satisfaction', 'Price', 
               'Features', 'Support Quality', 'Brand Awareness'],
    'our_company': [15, 4.2, 100, 8, 4.0, 60],
    'competitor_a': [25, 4.5, 90, 9, 4.3, 75],
    'competitor_b': [20, 4.0, 110, 7, 3.8, 55]
}

df = pd.DataFrame(competitor_data)

# Phân tích SWOT
strengths = []
weaknesses = []
opportunities = []
threats = []

for idx, row in df.iterrows():
    our_val = row['our_company']
    avg_competitor = (row['competitor_a'] + row['competitor_b']) / 2
    
    if our_val > avg_competitor * 1.1:
        strengths.append(f"{row['metric']}: {our_val} vs avg {avg_competitor:.1f}")
    elif our_val < avg_competitor * 0.9:
        weaknesses.append(f"{row['metric']}: {our_val} vs avg {avg_competitor:.1f}")
    
    # Opportunities: Competitor weaknesses
    if row['competitor_a'] < avg_competitor * 0.9:
        opportunities.append(f"Competitor A weak in {row['metric']}")
    
    # Threats: Competitor strengths
    if row['competitor_a'] > our_val * 1.1:
        threats.append(f"Competitor A strong in {row['metric']}")

print("STRENGTHS:")
for s in strengths:
    print(f"  ✓ {s}")

print("\nWEAKNESSES:")
for w in weaknesses:
    print(f"  ✗ {w}")

print("\nOPPORTUNITIES:")
for o in opportunities:
    print(f"  → {o}")

print("\nTHREATS:")
for t in threats:
    print(f"  ⚠ {t}")
```

## 🔍 Framework 3: RFM Analysis

**Mục đích**: Phân khúc customers dựa trên hành vi mua hàng

### Các Thành Phần

- **Recency (R)**: Lần mua gần nhất
- **Frequency (F)**: Tần suất mua
- **Monetary (M)**: Giá trị mua

### Ví Dụ: Phân Khúc Customers

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Tạo dữ liệu mẫu
np.random.seed(42)
dates = pd.date_range('2023-01-01', '2024-01-01', freq='D')
customer_ids = range(1, 1001)

transactions = []
for customer_id in customer_ids:
    num_transactions = np.random.poisson(5)  # Số lần mua
    for _ in range(num_transactions):
        transactions.append({
            'customer_id': customer_id,
            'date': np.random.choice(dates),
            'amount': np.random.lognormal(4, 1)  # Giá trị đơn hàng
        })

df = pd.DataFrame(transactions)
df['date'] = pd.to_datetime(df['date'])

# Tính RFM
analysis_date = df['date'].max()
rfm = df.groupby('customer_id').agg({
    'date': lambda x: (analysis_date - x.max()).days,  # Recency
    'customer_id': 'count',  # Frequency
    'amount': 'sum'  # Monetary
}).rename(columns={
    'date': 'recency',
    'customer_id': 'frequency',
    'amount': 'monetary'
})

# Chia thành 5 nhóm (1-5, 5 là tốt nhất)
rfm['R_score'] = pd.qcut(rfm['recency'], 5, labels=[5,4,3,2,1])
rfm['F_score'] = pd.qcut(rfm['frequency'].rank(method='first'), 5, labels=[1,2,3,4,5])
rfm['M_score'] = pd.qcut(rfm['monetary'], 5, labels=[1,2,3,4,5])

rfm['RFM_score'] = rfm['R_score'].astype(int) + rfm['F_score'].astype(int) + rfm['M_score'].astype(int)

# Phân khúc
def segment_customer(row):
    if row['RFM_score'] >= 13:
        return 'Champions'
    elif row['RFM_score'] >= 10:
        return 'Loyal Customers'
    elif row['R_score'] >= 4:
        return 'Potential Loyalists'
    elif row['F_score'] >= 3:
        return 'At Risk'
    elif row['R_score'] <= 2:
        return 'Lost'
    else:
        return 'Need Attention'

rfm['segment'] = rfm.apply(segment_customer, axis=1)

# Phân tích
rfm_reset = rfm.reset_index()  # Reset index để customer_id trở thành cột
segment_analysis = rfm_reset.groupby('segment').agg({
    'recency': 'mean',
    'frequency': 'mean',
    'monetary': 'mean',
    'customer_id': 'count'
}).round(2)

print(segment_analysis)

# Actionable Insights
print("\n=== ACTIONABLE INSIGHTS ===")
print(f"Champions ({len(rfm[rfm['segment']=='Champions'])} customers):")
print("  → Upsell/cross-sell, referral program")

print(f"\nAt Risk ({len(rfm[rfm['segment']=='At Risk'])} customers):")
print("  → Win-back campaign, special offers")

print(f"\nLost ({len(rfm[rfm['segment']=='Lost'])} customers):")
print("  → Re-engagement campaign hoặc chấp nhận churn")
```

## 🔍 Framework 4: Hypothesis-Driven Analysis

**Mục đích**: Phân tích dựa trên giả thuyết, tránh fishing for insights

### Quy Trình

1. **Formulate Hypothesis**: Đặt giả thuyết rõ ràng
2. **Define Metrics**: Xác định metrics để test
3. **Collect Data**: Thu thập dữ liệu cần thiết
4. **Test Hypothesis**: Phân tích và kiểm định
5. **Draw Conclusion**: Kết luận và đề xuất

### Ví Dụ: Test Giả Thuyết về Pricing

```python
import pandas as pd
import numpy as np
from scipy import stats

# Giả thuyết: Giảm giá 10% sẽ tăng số lượng đơn hàng ít nhất 15%

# Dữ liệu A/B test
np.random.seed(42)
control = {
    'price': 100,
    'orders': np.random.normal(1000, 100, 30)  # 30 ngày
}

treatment = {
    'price': 90,  # Giảm 10%
    'orders': np.random.normal(1200, 120, 30)  # Tăng ~20%
}

control_df = pd.DataFrame({
    'group': 'control',
    'price': control['price'],
    'orders': control['orders'],
    'revenue': control['orders'] * control['price']
})

treatment_df = pd.DataFrame({
    'group': 'treatment',
    'price': treatment['price'],
    'orders': treatment['orders'],
    'revenue': treatment['orders'] * treatment['price']
})

test_data = pd.concat([control_df, treatment_df], ignore_index=True)

# Phân tích
results = test_data.groupby('group').agg({
    'orders': ['mean', 'std', 'count'],
    'revenue': ['mean', 'sum']
}).round(2)

print("=== HYPOTHESIS TEST RESULTS ===")
print(results)

# Statistical test
control_orders = control_df['orders']
treatment_orders = treatment_df['orders']

t_stat, p_value = stats.ttest_ind(treatment_orders, control_orders)

print(f"\nT-test Results:")
print(f"  T-statistic: {t_stat:.3f}")
print(f"  P-value: {p_value:.4f}")

# Kết luận
order_increase = ((treatment_orders.mean() - control_orders.mean()) / control_orders.mean()) * 100
revenue_change = ((treatment_df['revenue'].sum() - control_df['revenue'].sum()) / control_df['revenue'].sum()) * 100

print(f"\n=== CONCLUSION ===")
print(f"Order increase: {order_increase:.1f}%")
print(f"Revenue change: {revenue_change:.1f}%")

if p_value < 0.05 and order_increase >= 15:
    print("✓ Hypothesis ACCEPTED: Giảm giá 10% tăng orders đáng kể")
    print("  → Recommendation: Triển khai giảm giá")
else:
    print("✗ Hypothesis REJECTED")
    print("  → Recommendation: Không nên giảm giá hoặc test thêm")
```

## 🔍 Framework 5: Before-After Analysis

**Mục đích**: Đánh giá tác động của thay đổi

### Ví Dụ: Đánh Giá Campaign Marketing

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

# Dữ liệu trước và sau campaign
dates = pd.date_range('2024-01-01', '2024-03-31', freq='D')
campaign_start = '2024-02-15'

# Tạo dữ liệu mẫu
np.random.seed(42)
baseline_sales = 1000
campaign_boost = 1.3  # Tăng 30%

sales_data = []
for date in dates:
    if date < pd.to_datetime(campaign_start):
        sales = np.random.normal(baseline_sales, 100)
    else:
        sales = np.random.normal(baseline_sales * campaign_boost, 120)
    
    sales_data.append({
        'date': date,
        'sales': max(0, sales),  # Đảm bảo không âm
        'period': 'Before' if date < pd.to_datetime(campaign_start) else 'After'
    })

df = pd.DataFrame(sales_data)

# Phân tích
before_after = df.groupby('period').agg({
    'sales': ['mean', 'std', 'sum']
}).round(2)

print("=== BEFORE-AFTER ANALYSIS ===")
print(before_after)

# Tính impact
before_mean = df[df['period'] == 'Before']['sales'].mean()
after_mean = df[df['period'] == 'After']['sales'].mean()
impact = ((after_mean - before_mean) / before_mean) * 100

print(f"\nImpact: {impact:.1f}% increase")

# Visualize
fig, ax = plt.subplots(figsize=(12, 6))
ax.plot(df['date'], df['sales'], alpha=0.6, linewidth=1)
ax.axvline(pd.to_datetime(campaign_start), color='red', linestyle='--', 
           label='Campaign Start')
ax.axhline(before_mean, color='blue', linestyle='--', alpha=0.5, label='Before Avg')
ax.axhline(after_mean, color='green', linestyle='--', alpha=0.5, label='After Avg')
ax.set_xlabel('Date')
ax.set_ylabel('Sales')
ax.set_title('Sales Before and After Campaign')
ax.legend()
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()

# Actionable Insights
print("\n=== ACTIONABLE INSIGHTS ===")
print(f"1. Campaign tăng sales {impact:.1f}%")
print(f"2. ROI cần tính: Chi phí campaign vs Doanh thu tăng thêm")
print(f"3. Duy trì hay không: Phân tích long-term impact")
```

## 🔍 Framework 6: Cohort Analysis

**Mục đích**: Phân tích retention và behavior theo từng nhóm users

### Ví Dụ: Retention Analysis

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Tạo dữ liệu cohort
np.random.seed(42)
cohorts = pd.date_range('2024-01-01', '2024-06-01', freq='MS')  # Monthly cohorts
user_ids = range(1, 5001)

user_cohorts = []
for user_id in user_ids:
    cohort = np.random.choice(cohorts)
    # Users có thể active trong nhiều tháng
    months_active = np.random.geometric(0.3)  # Retention probability
    for month_offset in range(min(months_active, 6)):  # Tối đa 6 tháng
        user_cohorts.append({
            'user_id': user_id,
            'cohort': cohort,
            'period': cohort + pd.DateOffset(months=month_offset),
            'active': True
        })

cohort_df = pd.DataFrame(user_cohorts)

# Tính retention
cohort_pivot = cohort_df.groupby(['cohort', 'period']).size().reset_index(name='users')
cohort_pivot['period_number'] = (cohort_pivot['period'] - cohort_pivot['cohort']).apply(
    lambda x: x.days // 30
)

# Pivot table
retention_table = cohort_pivot.pivot_table(
    index='cohort',
    columns='period_number',
    values='users',
    fill_value=0
)

# Tính retention rate (%)
retention_rate = retention_table.div(retention_table[0], axis=0) * 100

print("=== RETENTION RATE BY COHORT ===")
print(retention_rate.round(1))

# Visualize
plt.figure(figsize=(12, 8))
sns.heatmap(retention_rate, annot=True, fmt='.1f', cmap='YlOrRd', 
            cbar_kws={'label': 'Retention Rate (%)'})
plt.title('Cohort Retention Analysis')
plt.ylabel('Cohort')
plt.xlabel('Period Number')
plt.tight_layout()
plt.show()

# Insights
avg_retention = retention_rate.mean()
print(f"\n=== INSIGHTS ===")
print(f"Average Month 1 Retention: {avg_retention[1]:.1f}%")
print(f"Average Month 3 Retention: {avg_retention[3]:.1f}%")
print(f"Average Month 6 Retention: {avg_retention[6]:.1f}%")
```

## 🛠️ Kết Hợp Các Framework

Trong thực tế, bạn thường kết hợp nhiều framework:

**Ví dụ**: Phân tích product launch
1. **AARRR**: Xem funnel conversion
2. **Cohort Analysis**: Phân tích retention theo cohort
3. **Hypothesis Testing**: Test giả thuyết về feature impact
4. **Before-After**: So sánh trước và sau launch

## 📝 Bài Tập Thực Hành

### Bài Tập 1: AARRR Analysis
Phân tích một sản phẩm/servic và xác định điểm nghẽn trong funnel.

### Bài Tập 2: RFM Segmentation
Phân khúc customers và đề xuất chiến lược cho từng segment.

### Bài Tập 3: Hypothesis Testing
Đặt giả thuyết về một thay đổi và test bằng dữ liệu.

## 💡 Tips & Best Practices

1. **Chọn framework phù hợp**: Mỗi framework có mục đích riêng
2. **Kết hợp nhiều framework**: Không chỉ dùng một framework
3. **Visualize kết quả**: Charts giúp communicate tốt hơn
4. **Luôn có actionable insights**: Đừng chỉ báo cáo số liệu
5. **Document assumptions**: Ghi lại giả định khi phân tích

---

**Lưu ý**: Framework là công cụ, không phải mục đích. Quan trọng là bạn hiểu business context và đưa ra insights có giá trị.

