# Case Studies Thực Tế

## 🎯 Mục Tiêu

Sau bài học này, bạn sẽ:
- Học được cách áp dụng các framework vào tình huống thực tế
- Hiểu được quy trình phân tích từ đầu đến cuối
- Có thể tự phân tích các case study tương tự
- Rút ra lessons learned từ mỗi case

## 📚 Case Study 1: E-commerce Conversion Optimization

### Tình Huống

Một e-commerce platform có conversion rate thấp (2.5%) so với industry average (3.5%). CEO muốn tăng conversion rate lên ít nhất 3.5% trong 3 tháng.

### Bước 1: Thu Thập Dữ Liệu

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

# Tạo dữ liệu mẫu
np.random.seed(42)
n_visitors = 100000

# Dữ liệu visitor behavior
visitor_data = pd.DataFrame({
    'visitor_id': range(1, n_visitors + 1),
    'device': np.random.choice(['Desktop', 'Mobile', 'Tablet'], n_visitors, 
                              p=[0.4, 0.55, 0.05]),
    'traffic_source': np.random.choice(['Organic', 'Paid', 'Direct', 'Social'], 
                                      n_visitors, p=[0.4, 0.3, 0.2, 0.1]),
    'page_load_time': np.random.exponential(3, n_visitors),  # seconds
    'pages_viewed': np.random.poisson(4, n_visitors),
    'time_on_site': np.random.exponential(180, n_visitors),  # seconds
})

# Conversion (phụ thuộc vào các factors)
conversion_prob = (
    0.02 +  # Base conversion
    (visitor_data['device'] == 'Desktop') * 0.01 +  # Desktop tốt hơn
    (visitor_data['page_load_time'] < 2) * 0.01 +  # Fast load tốt hơn
    (visitor_data['pages_viewed'] > 3) * 0.01 +  # Nhiều pages tốt hơn
    np.random.normal(0, 0.005, n_visitors)  # Noise
)

visitor_data['converted'] = (np.random.random(n_visitors) < conversion_prob).astype(int)
visitor_data['order_value'] = visitor_data['converted'] * np.random.lognormal(13, 0.5, n_visitors)

print("=== DATA OVERVIEW ===")
print(f"Total visitors: {len(visitor_data):,}")
print(f"Conversions: {visitor_data['converted'].sum():,}")
print(f"Conversion rate: {visitor_data['converted'].mean()*100:.2f}%")
print(f"Total revenue: {visitor_data['order_value'].sum():,.0f} VND")
```

### Bước 2: Phân Tích Nguyên Nhân (Root Cause Analysis)

```python
# Phân tích conversion rate theo các dimensions
print("\n=== CONVERSION RATE BY DEVICE ===")
device_analysis = visitor_data.groupby('device').agg({
    'visitor_id': 'count',
    'converted': 'sum',
    'order_value': 'sum'
}).rename(columns={'visitor_id': 'visitors'})
device_analysis['conversion_rate'] = (device_analysis['converted'] / 
                                      device_analysis['visitors'] * 100).round(2)
print(device_analysis)

print("\n=== CONVERSION RATE BY TRAFFIC SOURCE ===")
source_analysis = visitor_data.groupby('traffic_source').agg({
    'visitor_id': 'count',
    'converted': 'sum'
}).rename(columns={'visitor_id': 'visitors'})
source_analysis['conversion_rate'] = (source_analysis['converted'] / 
                                      source_analysis['visitors'] * 100).round(2)
print(source_analysis)

# Phân tích page load time
print("\n=== PAGE LOAD TIME ANALYSIS ===")
fast_load = visitor_data[visitor_data['page_load_time'] < 2]
slow_load = visitor_data[visitor_data['page_load_time'] >= 2]

print(f"Fast load (<2s): {fast_load['converted'].mean()*100:.2f}% conversion")
print(f"Slow load (>=2s): {slow_load['converted'].mean()*100:.2f}% conversion")

# Statistical test
t_stat, p_value = stats.ttest_ind(
    fast_load['converted'], 
    slow_load['converted']
)
print(f"T-test p-value: {p_value:.4f}")
if p_value < 0.05:
    print("→ Page load time có ảnh hưởng đáng kể đến conversion")
```

### Bước 3: Xác Định Vấn Đề Chính

```python
# 5 Whys Analysis
print("\n=== 5 WHYS ANALYSIS ===")
print("1. Tại sao conversion rate thấp?")
print("   → Mobile conversion rate thấp (2.1% vs Desktop 3.5%)")
print("\n2. Tại sao mobile conversion thấp?")
print("   → Page load time trên mobile cao (trung bình 4.5s)")
print("\n3. Tại sao page load time cao?")
print("   → Images không được optimize, không có lazy loading")
print("\n4. Tại sao không optimize?")
print("   → Thiếu technical resources và priority")
print("\n5. Tại sao thiếu priority?")
print("   → Không có data để chứng minh impact của page speed")

# Root cause
print("\n=== ROOT CAUSE ===")
print("1. Mobile page load time cao (4.5s vs target 2s)")
print("2. Mobile checkout flow phức tạp (5 steps)")
print("3. Thiếu mobile-specific optimizations")
```

### Bước 4: Đưa Ra Giải Pháp

```python
# Tính toán impact potential
current_mobile_visitors = len(visitor_data[visitor_data['device'] == 'Mobile'])
current_mobile_conversion = visitor_data[visitor_data['device'] == 'Mobile']['converted'].mean()
target_conversion = 0.035  # 3.5%

potential_conversions = current_mobile_visitors * target_conversion
current_conversions = current_mobile_visitors * current_mobile_conversion
additional_conversions = potential_conversions - current_conversions

avg_order_value = visitor_data[visitor_data['converted'] == 1]['order_value'].mean()
revenue_opportunity = additional_conversions * avg_order_value

print("=== SOLUTION & IMPACT ===")
print(f"\nCurrent mobile conversion: {current_mobile_conversion*100:.2f}%")
print(f"Target conversion: {target_conversion*100:.2f}%")
print(f"Additional conversions: {additional_conversions:,.0f}")
print(f"Revenue opportunity: {revenue_opportunity:,.0f} VND/month")

print("\n=== RECOMMENDED ACTIONS ===")
print("1. Optimize mobile page speed (Week 1-2):")
print("   - Compress images (target: <200KB)")
print("   - Implement lazy loading")
print("   - Minify CSS/JS")
print("   - Expected: Page load < 2s, +0.5% conversion")

print("\n2. Simplify mobile checkout (Week 3-4):")
print("   - Reduce steps from 5 to 2")
print("   - Add autofill")
print("   - Expected: +0.8% conversion")

print("\n3. Mobile-specific optimizations (Week 5-6):")
print("   - Larger touch targets")
print("   - Better form UX")
print("   - Expected: +0.2% conversion")

print(f"\nTotal expected improvement: +1.5% conversion")
print(f"New conversion rate: {(current_mobile_conversion + 0.015)*100:.2f}%")
```

### Bước 5: Implementation & Monitoring

```python
# Success metrics
print("\n=== SUCCESS METRICS ===")
print("Primary KPI:")
print("  - Overall conversion rate: 2.5% → 3.5%")
print("\nSecondary KPIs:")
print("  - Mobile conversion rate: 2.1% → 3.0%")
print("  - Page load time: 4.5s → <2s")
print("  - Mobile checkout completion: 60% → 80%")
print("\nMonitoring:")
print("  - Weekly conversion rate tracking")
print("  - A/B test results")
print("  - User feedback")

# Timeline
print("\n=== TIMELINE ===")
print("Week 1-2: Page speed optimization")
print("Week 3-4: Checkout simplification")
print("Week 5-6: Mobile UX improvements")
print("Week 7-12: Monitor, iterate, optimize")
```

## 📚 Case Study 2: Customer Churn Analysis

### Tình Huống

Một subscription service có churn rate tăng từ 5% lên 12% trong quý vừa qua. Cần phân tích nguyên nhân và đưa ra giải pháp.

### Phân Tích

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

# Tạo dữ liệu customers
np.random.seed(42)
n_customers = 5000

customer_data = pd.DataFrame({
    'customer_id': range(1, n_customers + 1),
    'signup_date': pd.to_datetime(np.random.choice(
        pd.date_range('2023-01-01', '2024-01-01', freq='D'), n_customers
    )),
    'plan': np.random.choice(['Basic', 'Premium', 'Enterprise'], 
                            n_customers, p=[0.5, 0.4, 0.1]),
    'monthly_revenue': np.random.choice([500000, 1000000, 2000000], 
                                       n_customers, p=[0.5, 0.4, 0.1]),
    'support_tickets': np.random.poisson(2, n_customers),
    'feature_usage': np.random.uniform(0, 1, n_customers),
    'satisfaction_score': np.random.uniform(1, 5, n_customers),
})

# Churn probability (phụ thuộc vào các factors)
churn_prob = (
    0.05 +  # Base churn
    (customer_data['support_tickets'] > 3) * 0.1 +  # Nhiều tickets
    (customer_data['satisfaction_score'] < 2.5) * 0.15 +  # Low satisfaction
    (customer_data['feature_usage'] < 0.3) * 0.1 +  # Low usage
    np.random.normal(0, 0.02, n_customers)  # Noise
)
churn_prob = np.clip(churn_prob, 0, 1)

customer_data['churned'] = (np.random.random(n_customers) < churn_prob).astype(int)

# Analysis
print("=== CHURN ANALYSIS ===")
print(f"Total customers: {len(customer_data):,}")
print(f"Churned customers: {customer_data['churned'].sum():,}")
print(f"Churn rate: {customer_data['churned'].mean()*100:.2f}%")

# Churn by plan
print("\n=== CHURN BY PLAN ===")
churn_by_plan = customer_data.groupby('plan').agg({
    'customer_id': 'count',
    'churned': 'sum',
    'monthly_revenue': 'mean'
}).rename(columns={'customer_id': 'total'})
churn_by_plan['churn_rate'] = (churn_by_plan['churned'] / 
                               churn_by_plan['total'] * 100).round(2)
print(churn_by_plan)

# Churn by satisfaction
print("\n=== CHURN BY SATISFACTION ===")
customer_data['satisfaction_bucket'] = pd.cut(
    customer_data['satisfaction_score'], 
    bins=[0, 2, 3, 4, 5],
    labels=['Very Low (1-2)', 'Low (2-3)', 'Medium (3-4)', 'High (4-5)']
)
churn_by_satisfaction = customer_data.groupby('satisfaction_bucket').agg({
    'customer_id': 'count',
    'churned': 'sum'
}).rename(columns={'customer_id': 'total'})
churn_by_satisfaction['churn_rate'] = (churn_by_satisfaction['churned'] / 
                                       churn_by_satisfaction['total'] * 100).round(2)
print(churn_by_satisfaction)

# Root cause analysis
print("\n=== ROOT CAUSE ANALYSIS (5 Whys) ===")
print("1. Tại sao churn rate tăng?")
print("   → Nhiều customers không hài lòng (satisfaction < 3)")
print("\n2. Tại sao không hài lòng?")
print("   → Support tickets tăng, response time chậm")
print("\n3. Tại sao support chậm?")
print("   → Support team thiếu nhân lực (từ 10 → 5 người)")
print("\n4. Tại sao thiếu nhân lực?")
print("   → Budget cut, không hire thêm")
print("\n5. Tại sao budget cut?")
print("   → Không có data chứng minh ROI của support team")

# Solutions
print("\n=== RECOMMENDED SOLUTIONS ===")
high_risk = customer_data[
    (customer_data['satisfaction_score'] < 2.5) | 
    (customer_data['support_tickets'] > 3)
]
revenue_at_risk = high_risk['monthly_revenue'].sum()

print(f"High-risk customers: {len(high_risk):,}")
print(f"Revenue at risk: {revenue_at_risk:,.0f} VND/month")

print("\n1. Immediate actions (this week):")
print("   - Reach out to high-risk customers")
print("   - Offer personalized support")
print("   - Discount for next 3 months")
print("   - Expected: Reduce churn by 30%")

print("\n2. Short-term (this month):")
print("   - Hire 3 more support agents")
print("   - Improve response time SLA")
print("   - Expected: Reduce churn by additional 20%")

print("\n3. Long-term (this quarter):")
print("   - Implement self-service portal")
print("   - Proactive customer success program")
print("   - Expected: Reduce churn to 5%")
```

## 📚 Case Study 3: Pricing Strategy Optimization

### Tình Huống

Một SaaS company muốn tối ưu pricing để tăng revenue. Hiện tại có 3 plans: Basic ($50), Pro ($100), Enterprise ($200).

### Phân Tích

```python
# Pricing elasticity analysis
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Dữ liệu sales theo price
pricing_experiments = pd.DataFrame({
    'price_basic': [40, 50, 60, 70],
    'price_pro': [80, 100, 120, 140],
    'units_basic': [2000, 1500, 1000, 700],
    'units_pro': [800, 600, 400, 250],
    'units_enterprise': [100, 100, 100, 100]  # Enterprise không đổi
})

pricing_experiments['revenue_basic'] = (
    pricing_experiments['price_basic'] * pricing_experiments['units_basic']
)
pricing_experiments['revenue_pro'] = (
    pricing_experiments['price_pro'] * pricing_experiments['units_pro']
)
pricing_experiments['revenue_enterprise'] = (
    200 * pricing_experiments['units_enterprise']
)
pricing_experiments['total_revenue'] = (
    pricing_experiments['revenue_basic'] + 
    pricing_experiments['revenue_pro'] + 
    pricing_experiments['revenue_enterprise']
)

print("=== PRICING EXPERIMENT RESULTS ===")
print(pricing_experiments)

# Find optimal pricing
optimal_idx = pricing_experiments['total_revenue'].idxmax()
optimal = pricing_experiments.loc[optimal_idx]

print(f"\n=== OPTIMAL PRICING ===")
print(f"Basic: ${optimal['price_basic']:.0f}")
print(f"Pro: ${optimal['price_pro']:.0f}")
print(f"Enterprise: $200 (unchanged)")
print(f"Total revenue: ${optimal['total_revenue']:,.0f}")

# Visualize
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

axes[0].plot(pricing_experiments['price_basic'], 
             pricing_experiments['units_basic'], 
             marker='o', label='Basic', linewidth=2)
axes[0].plot(pricing_experiments['price_pro'], 
             pricing_experiments['units_pro'], 
             marker='s', label='Pro', linewidth=2)
axes[0].set_xlabel('Price ($)', fontsize=12)
axes[0].set_ylabel('Units Sold', fontsize=12)
axes[0].set_title('Price Elasticity', fontsize=14, fontweight='bold')
axes[0].legend()
axes[0].grid(True, alpha=0.3)

axes[1].plot(range(len(pricing_experiments)), 
             pricing_experiments['total_revenue'],
             marker='o', linewidth=2, markersize=8)
axes[1].axvline(optimal_idx, color='red', linestyle='--', 
                label=f'Optimal (${optimal["total_revenue"]:,.0f})')
axes[1].set_xlabel('Experiment', fontsize=12)
axes[1].set_ylabel('Total Revenue ($)', fontsize=12)
axes[1].set_title('Total Revenue by Pricing Strategy', fontsize=14, fontweight='bold')
axes[1].set_xticks(range(len(pricing_experiments)))
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()

# Recommendations
current_revenue = pricing_experiments.loc[1, 'total_revenue']  # Current pricing
improvement = optimal['total_revenue'] - current_revenue
improvement_pct = (improvement / current_revenue * 100)

print("\n=== RECOMMENDATIONS ===")
print(f"Current revenue: ${current_revenue:,.0f}")
print(f"Optimal revenue: ${optimal['total_revenue']:,.0f}")
print(f"Potential improvement: ${improvement:,.0f} ({improvement_pct:.1f}%)")
print("\nActions:")
print("1. Increase Basic price to $60 (+20%)")
print("2. Increase Pro price to $120 (+20%)")
print("3. Monitor churn rate closely")
print("4. A/B test before full rollout")
```

## 💡 Lessons Learned

### Từ Case Study 1 (Conversion Optimization):
- Mobile optimization là critical
- Page speed có impact lớn đến conversion
- Cần data để justify technical investments

### Từ Case Study 2 (Churn Analysis):
- Customer satisfaction là leading indicator của churn
- Support quality ảnh hưởng trực tiếp đến retention
- Proactive intervention có thể giảm churn đáng kể

### Từ Case Study 3 (Pricing):
- Pricing optimization có thể tăng revenue đáng kể
- Cần test trước khi implement
- Monitor churn khi tăng giá

## 📝 Bài Tập Thực Hành

Chọn một case study và phân tích đầy đủ:
1. Thu thập dữ liệu
2. Phân tích nguyên nhân
3. Đưa ra giải pháp
4. Tính toán impact
5. Tạo action plan

---

**Lưu ý**: Case studies giúp bạn học từ thực tế. Hãy practice phân tích nhiều case studies khác nhau để phát triển tư duy phân tích!

