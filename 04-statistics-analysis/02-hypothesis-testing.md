# 02. Hypothesis Testing

## Mục Tiêu
- Hiểu được khái niệm hypothesis testing và khi nào sử dụng
- Nắm vững các loại test phổ biến: t-test, chi-square, ANOVA
- Biết cách interpret p-value và significance level
- Áp dụng vào các tình huống thực tế: A/B testing, so sánh nhóm

## Hypothesis Testing Là Gì?

**Hypothesis testing (kiểm định giả thuyết)** là phương pháp thống kê để:
- **Ra quyết định** dựa trên dữ liệu
- **Kiểm tra** xem một claim có đúng không
- **So sánh** các nhóm với nhau

**Ví dụ thực tế**:
- "Campaign mới có tăng conversion rate không?"
- "Sản phẩm A có tốt hơn sản phẩm B không?"
- "Giá mới có ảnh hưởng đến doanh số không?"

## Các Khái Niệm Cơ Bản

### Null Hypothesis (H0) và Alternative Hypothesis (H1)

- **H0 (Null Hypothesis)**: Giả thuyết mặc định, thường là "không có sự khác biệt"
- **H1 (Alternative Hypothesis)**: Giả thuyết bạn muốn chứng minh

**Ví dụ đơn giản**:
- **H0**: Mean doanh số = 50,000,000 VND
- **H1**: Mean doanh số ≠ 50,000,000 VND

```python
from scipy import stats
import numpy as np
import pandas as pd

# Ví dụ đơn giản
# H0: Mean = 50,000,000
# H1: Mean ≠ 50,000,000
sample = np.random.normal(52_000_000, 10_000_000, 100)
t_stat, p_value = stats.ttest_1samp(sample, 50_000_000)

print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")
if p_value < 0.05:
    print("→ Bác bỏ H0: Mean khác 50,000,000")
else:
    print("→ Không đủ bằng chứng bác bỏ H0")
```

### P-value (Giá Trị P)

**P-value** là xác suất quan sát được kết quả như vậy (hoặc cực đoan hơn) nếu H0 đúng.

**Cách hiểu**:
- **P-value < 0.05**: Có bằng chứng mạnh để bác bỏ H0 → Kết quả có ý nghĩa thống kê
- **P-value >= 0.05**: Không đủ bằng chứng để bác bỏ H0 → Kết quả không có ý nghĩa thống kê

**Lưu ý quan trọng**:
- P-value < 0.05 **KHÔNG** có nghĩa là "H1 đúng 95%"
- P-value < 0.05 có nghĩa là "nếu H0 đúng, xác suất quan sát được kết quả này < 5%"

### Significance Level (α - Alpha)

**Significance level (mức ý nghĩa)** là ngưỡng bạn chọn để quyết định bác bỏ H0.

- **α = 0.05** (5%): Phổ biến nhất
- **α = 0.01** (1%): Nghiêm ngặt hơn
- **α = 0.10** (10%): Ít nghiêm ngặt hơn

**Quy tắc**:
- Nếu **p-value < α**: Bác bỏ H0
- Nếu **p-value >= α**: Không bác bỏ H0

### Type I và Type II Errors

**Type I Error (Lỗi Loại I)**: Bác bỏ H0 khi H0 đúng
- Xác suất = α (significance level)
- Ví dụ: Kết luận campaign có hiệu quả khi thực ra không

**Type II Error (Lỗi Loại II)**: Không bác bỏ H0 khi H0 sai
- Xác suất = β
- Ví dụ: Kết luận campaign không hiệu quả khi thực ra có

**Power of a Test (Lực Kiểm Định)**: 1 - β
- Xác suất bác bỏ H0 khi H0 thực sự sai
- Power cao = Dễ phát hiện sự khác biệt thực sự

## Các Loại Test Phổ Biến

### 1. One-Sample T-Test

**Mục đích**: So sánh mean của một sample với một giá trị cụ thể

**Ví dụ**: Doanh số trung bình có bằng 50 triệu không?

```python
from scipy import stats
import numpy as np

# Dữ liệu doanh số (triệu VND)
sales = np.array([45, 52, 48, 55, 50, 53, 49, 51, 47, 54])

# H0: Mean = 50
# H1: Mean ≠ 50
t_stat, p_value = stats.ttest_1samp(sales, 50)

print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")
print(f"Mean: {sales.mean():.2f}")

if p_value < 0.05:
    print("→ Bác bỏ H0: Mean khác 50 triệu VND")
else:
    print("→ Không đủ bằng chứng: Mean có thể bằng 50 triệu VND")
```

### 2. Two-Sample T-Test

**Mục đích**: So sánh mean của 2 nhóm độc lập

**Ví dụ**: Conversion rate của campaign A có khác campaign B không?

```python
import pandas as pd
import numpy as np
from scipy import stats

# Dữ liệu A/B test
np.random.seed(42)
campaign_a = np.random.normal(0.03, 0.01, 1000)  # Conversion rate 3%
campaign_b = np.random.normal(0.035, 0.01, 1000)  # Conversion rate 3.5%

# H0: Mean A = Mean B
# H1: Mean A ≠ Mean B
t_stat, p_value = stats.ttest_ind(campaign_a, campaign_b)

print("=== A/B TEST RESULTS ===")
print(f"Campaign A - Mean: {campaign_a.mean():.4f}, Std: {campaign_a.std():.4f}")
print(f"Campaign B - Mean: {campaign_b.mean():.4f}, Std: {campaign_b.std():.4f}")
print(f"\nT-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("\n→ Bác bỏ H0: Campaign B có conversion rate cao hơn đáng kể")
    improvement = ((campaign_b.mean() - campaign_a.mean()) / campaign_a.mean()) * 100
    print(f"→ Cải thiện: {improvement:.1f}%")
else:
    print("\n→ Không có sự khác biệt đáng kể giữa 2 campaigns")
```

### 3. Paired T-Test

**Mục đích**: So sánh mean của 2 nhóm có liên quan (trước/sau, cùng đối tượng)

**Ví dụ**: Doanh số trước và sau campaign có khác nhau không?

```python
# Dữ liệu trước và sau campaign (cùng stores)
before = np.array([100, 105, 98, 102, 110, 95, 108, 103, 97, 104])
after = np.array([115, 120, 112, 118, 125, 110, 122, 117, 111, 119])

# H0: Mean difference = 0
# H1: Mean difference ≠ 0
t_stat, p_value = stats.ttest_rel(after, before)

print("=== BEFORE-AFTER ANALYSIS ===")
print(f"Before - Mean: {before.mean():.2f}")
print(f"After - Mean: {after.mean():.2f}")
print(f"Difference: {after.mean() - before.mean():.2f}")
print(f"\nT-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    improvement = ((after.mean() - before.mean()) / before.mean()) * 100
    print(f"\n→ Campaign có hiệu quả: Tăng {improvement:.1f}%")
else:
    print("\n→ Campaign không có hiệu quả đáng kể")
```

### 4. Chi-Square Test

**Mục đích**: Kiểm tra mối quan hệ giữa 2 biến categorical

**Ví dụ**: Nguồn traffic có liên quan đến conversion không?

```python
from scipy.stats import chi2_contingency
import pandas as pd
import numpy as np

# Dữ liệu: Traffic source vs Conversion
np.random.seed(42)
data = {
    'source': ['Google', 'Facebook', 'Email', 'Direct'] * 250,
    'converted': np.random.choice([True, False], 1000, p=[0.03, 0.97])
}

df = pd.DataFrame(data)

# Tạo contingency table
contingency = pd.crosstab(df['source'], df['converted'])
print("=== CONTINGENCY TABLE ===")
print(contingency)

# Chi-square test
chi2, p_value, dof, expected = chi2_contingency(contingency)

print(f"\nChi-square statistic: {chi2:.3f}")
print(f"P-value: {p_value:.4f}")
print(f"Degrees of freedom: {dof}")

if p_value < 0.05:
    print("\n→ Có mối quan hệ giữa source và conversion")
    # Phân tích chi tiết
    conversion_by_source = df.groupby('source')['converted'].mean()
    print("\nConversion rate by source:")
    print(conversion_by_source.sort_values(ascending=False))
else:
    print("\n→ Không có mối quan hệ giữa source và conversion")
```

### 5. ANOVA (Analysis of Variance)

**Mục đích**: So sánh mean của 3+ nhóm

**Ví dụ**: Doanh số có khác nhau giữa các regions không?

```python
from scipy.stats import f_oneway
import numpy as np

# Dữ liệu doanh số theo region
np.random.seed(42)
north = np.random.normal(100, 10, 50)
central = np.random.normal(105, 12, 50)
south = np.random.normal(98, 11, 50)

# H0: Tất cả means bằng nhau
# H1: Ít nhất một mean khác
f_stat, p_value = f_oneway(north, central, south)

print("=== ANOVA TEST ===")
print(f"North - Mean: {north.mean():.2f}, Std: {north.std():.2f}")
print(f"Central - Mean: {central.mean():.2f}, Std: {central.std():.2f}")
print(f"South - Mean: {south.mean():.2f}, Std: {south.std():.2f}")
print(f"\nF-statistic: {f_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("\n→ Có sự khác biệt đáng kể giữa các regions")
    # Post-hoc test để xem region nào khác
    from scipy.stats import ttest_ind
    print("\nPairwise comparisons:")
    t_stat_nc, p_nc = ttest_ind(north, central)
    t_stat_ns, p_ns = ttest_ind(north, south)
    t_stat_cs, p_cs = ttest_ind(central, south)
    print(f"North vs Central: p-value = {p_nc:.4f}")
    print(f"North vs South: p-value = {p_ns:.4f}")
    print(f"Central vs South: p-value = {p_cs:.4f}")
else:
    print("\n→ Không có sự khác biệt đáng kể giữa các regions")
```

## A/B Testing Thực Tế

### Case Study: Test Landing Page Mới

```python
import pandas as pd
import numpy as np
from scipy import stats

# Dữ liệu A/B test: Landing page cũ vs mới
np.random.seed(42)
n_visitors = 5000

# Control group (landing page cũ)
control = pd.DataFrame({
    'visitor_id': range(1, n_visitors + 1),
    'group': 'control',
    'converted': np.random.choice([True, False], n_visitors, p=[0.025, 0.975]),
    'time_on_page': np.random.normal(45, 15, n_visitors)
})

# Treatment group (landing page mới)
treatment = pd.DataFrame({
    'visitor_id': range(n_visitors + 1, 2 * n_visitors + 1),
    'group': 'treatment',
    'converted': np.random.choice([True, False], n_visitors, p=[0.032, 0.968]),
    'time_on_page': np.random.normal(55, 18, n_visitors)
})

test_data = pd.concat([control, treatment], ignore_index=True)

# Phân tích conversion rate
conversion_summary = test_data.groupby('group').agg({
    'converted': ['mean', 'sum', 'count']
}).round(4)
conversion_summary.columns = ['conversion_rate', 'conversions', 'visitors']

print("=== CONVERSION RATE SUMMARY ===")
print(conversion_summary)

# Statistical test
control_converted = control['converted']
treatment_converted = treatment['converted']

# Chi-square test (hoặc two-proportion z-test)
from scipy.stats import chi2_contingency
contingency = pd.crosstab(test_data['group'], test_data['converted'])
chi2, p_value, dof, expected = chi2_contingency(contingency)

print(f"\nChi-square test:")
print(f"  Chi-square: {chi2:.3f}")
print(f"  P-value: {p_value:.4f}")

# Tính improvement
control_rate = control_converted.mean()
treatment_rate = treatment_converted.mean()
improvement = ((treatment_rate - control_rate) / control_rate) * 100

print(f"\n=== CONCLUSION ===")
if p_value < 0.05:
    print(f"✓ Landing page mới có conversion rate cao hơn đáng kể")
    print(f"  Improvement: {improvement:.1f}%")
    print(f"  Control: {control_rate*100:.2f}%")
    print(f"  Treatment: {treatment_rate*100:.2f}%")
    print(f"\n→ Recommendation: Triển khai landing page mới")
else:
    print(f"✗ Không có sự khác biệt đáng kể")
    print(f"  Control: {control_rate*100:.2f}%")
    print(f"  Treatment: {treatment_rate*100:.2f}%")
    print(f"\n→ Recommendation: Cần test thêm hoặc giữ nguyên")
```

## Điều Kiện Sử Dụng Các Test

### T-Test
- Dữ liệu **numeric**
- Phân bố **gần như normal** (hoặc sample size lớn)
- **Independent samples** (với two-sample t-test)

### Chi-Square Test
- Dữ liệu **categorical**
- **Expected frequency >= 5** trong mỗi cell

### ANOVA
- Dữ liệu **numeric**
- Phân bố **gần như normal**
- **Variance bằng nhau** giữa các nhóm (homogeneity of variance)

## Best Practices

1. **Kiểm tra assumptions trước**: Normal distribution, equal variance, etc.
2. **Chọn test phù hợp**: Dựa trên loại dữ liệu và câu hỏi
3. **Interpret kết quả đúng**: P-value < 0.05 không có nghĩa là "hiệu quả 95%"
4. **Consider effect size**: Không chỉ p-value, mà còn độ lớn của sự khác biệt
5. **Multiple testing correction**: Nếu test nhiều lần, cần điều chỉnh α

## 📝 Bài Tập Thực Hành

### Bài Tập 1: A/B Test Analysis
Phân tích A/B test với dữ liệu conversion rate và đưa ra kết luận.

### Bài Tập 2: Before-After Analysis
So sánh doanh số trước và sau campaign sử dụng paired t-test.

### Bài Tập 3: Multi-Group Comparison
So sánh performance của 4 sản phẩm khác nhau sử dụng ANOVA.

---

**Lưu ý**: Hypothesis testing là công cụ mạnh mẽ, nhưng cần hiểu đúng và sử dụng đúng cách. Luôn kết hợp với domain knowledge và business context.

