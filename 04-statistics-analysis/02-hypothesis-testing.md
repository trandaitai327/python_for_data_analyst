# 02. Hypothesis Testing

## Mục Tiêu
- Hiểu rõ bản chất **hypothesis testing** (kiểm định giả thuyết)
- Biết phân biệt H0, H1, p-value, α, power, type I & type II error
- Thực hiện t-test, chi-square test, ANOVA bằng Python
- Chọn test phù hợp với từng loại dữ liệu và câu hỏi business
- Áp dụng vào phân tích dữ liệu thực tế (A/B test, so sánh nhóm,…)

## Hypothesis Testing Là Gì?

**Hypothesis testing** là khung tư duy để:
- **Đưa ra quyết định** dựa trên dữ liệu, thay vì cảm tính
- **Kiểm chứng giả thuyết** business/ sản phẩm:  
  "Chiến dịch marketing có hiệu quả không?",  
  "Version B có tốt hơn Version A không?"
- **So sánh nhóm**:  
  "Doanh số 2 khu vực có khác nhau không?",  
  "Retention của cohort mới có cao hơn không?"

Thay vì trả lời “có/không” một cách tuyệt đối, kiểm định giả thuyết trả lời:
> “Với dữ liệu hiện có, **có đủ bằng chứng** để tin rằng có sự khác biệt hay không?”

## 1. Khái Niệm Cơ Bản

### Null Hypothesis (H0) và Alternative Hypothesis (H1)

- **H0 (Null)**: Giả thuyết “không có gì xảy ra”
  - Không có sự khác biệt
  - Không có ảnh hưởng
  - Không có mối quan hệ
- **H1 (Alternative)**: Giả thuyết bạn muốn chứng minh
  - Có sự khác biệt
  - Có ảnh hưởng
  - Có mối quan hệ

Ví dụ:
- H0: “Conversion rate của A và B **bằng nhau**”
- H1: “Conversion rate của A và B **khác nhau**”

### P-value và Significance Level

- **P-value**: Xác suất quan sát được kết quả **ít nhất cực đoan như hiện tại** nếu H0 đúng
- **Significance level (α)**: Ngưỡng quyết định, thường là 0.05 (5%)
- **Quy tắc quyết định**:
  - Nếu **p-value < α** → Bác bỏ H0 → Có đủ bằng chứng ủng hộ H1
  - Nếu **p-value ≥ α** → Không đủ bằng chứng để bác bỏ H0 (không có nghĩa là H0 đúng)

### Type I & Type II Error

- **Type I error (False Positive)**:
  - Bác bỏ H0 khi H0 **thực ra đúng**
  - Xác suất = α (ví dụ α = 0.05 → chấp nhận 5% “báo động giả”)
- **Type II error (False Negative)**:
  - Không bác bỏ H0 khi H1 **thực ra đúng**
  - Xác suất ký hiệu β

### Power of a Test (Độ Mạnh Của Kiểm Định)

- **Power = 1 − β**: Xác suất **phát hiện được hiệu ứng khi nó thật sự tồn tại**
- Power càng cao:
  - Càng ít bỏ sót cơ hội tốt
  - Cần **sample size đủ lớn**

Trong thực tế A/B test, bạn cần:
- Đặt trước α (thường 0.05)
- Chọn mức power mong muốn (thường 0.8 hoặc 0.9)
- Tính **sample size cần thiết** trước khi chạy thử nghiệm

```python
from scipy import stats
import numpy as np
import pandas as pd

# Ví dụ đơn giản
# H0: Mean = 50000000
# H1: Mean ≠ 50000000
sample = np.random.normal(52000000, 10000000, 100)
t_stat, p_value = stats.ttest_1samp(sample, 50000000)

print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")
if p_value < 0.05:
    print("→ Bác bỏ H0: Mean khác 50000000")
else:
    print("→ Không đủ bằng chứng bác bỏ H0")
```

## 2. One-Sample T-Test

Kiểm tra xem mean của một mẫu có khác với giá trị cụ thể không.

Điều kiện (xấp xỉ):
- Dữ liệu numeric, gần phân bố chuẩn (hoặc sample size đủ lớn – CLT)
- Các quan sát độc lập với nhau

```python
# Ví dụ: Kiểm tra xem doanh số trung bình có = 50 triệu không?
sales_sample = np.random.normal(52000000, 10000000, 50)

# One-sample t-test
t_stat, p_value = stats.ttest_1samp(sales_sample, 50000000)

print("One-Sample T-Test")
print(f"Sample mean: {np.mean(sales_sample):,.0f}")
print(f"Hypothesized mean: 50,000,000")
print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Bác bỏ H0: Mean khác 50 triệu")
else:
    print("→ Không đủ bằng chứng: Mean có thể = 50 triệu")
```

**Ví dụ thực tế**: Kiểm tra target
```python
# Kiểm tra xem doanh số tháng này có đạt target 60 triệu không?
monthly_sales = np.random.normal(58000000, 8000000, 30)

t_stat, p_value = stats.ttest_1samp(monthly_sales, 60000000)

print("Kiểm tra target doanh số:")
print(f"Doanh số trung bình: {np.mean(monthly_sales):,.0f} VND")
print(f"Target: 60,000,000 VND")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    if np.mean(monthly_sales) < 60000000:
        print("→ Không đạt target (thấp hơn đáng kể)")
    else:
        print("→ Vượt target (cao hơn đáng kể)")
else:
    print("→ Đạt target (không khác biệt đáng kể)")
```

## 3. Two-Sample T-Test

So sánh mean của 2 nhóm độc lập.

Các biến thể:
- **Independent t-test**: 2 nhóm độc lập (khu vực A vs B)
- **Welch’s t-test**: Dùng khi variance 2 nhóm khác nhau đáng kể

Giả định chính:
- Dữ liệu numeric, gần chuẩn
- Các quan sát độc lập giữa 2 nhóm

```python
# Ví dụ: So sánh doanh số 2 khu vực
north_sales = np.random.normal(50000000, 10000000, 50)
south_sales = np.random.normal(55000000, 12000000, 50)

# Two-sample t-test
t_stat, p_value = stats.ttest_ind(north_sales, south_sales)

print("Two-Sample T-Test")
print(f"North mean: {np.mean(north_sales):,.0f}")
print(f"South mean: {np.mean(south_sales):,.0f}")
print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Có sự khác biệt đáng kể giữa 2 khu vực")
else:
    print("→ Không có sự khác biệt đáng kể")
```

**Ví dụ thực tế**: So sánh hiệu quả chiến dịch
```python
# So sánh doanh số trước và sau chiến dịch
before_campaign = np.random.normal(50000000, 8000000, 30)
after_campaign = np.random.normal(58000000, 9000000, 30)

t_stat, p_value = stats.ttest_ind(after_campaign, before_campaign)

print("Đánh giá hiệu quả chiến dịch:")
print(f"Trước chiến dịch: {np.mean(before_campaign):,.0f} VND")
print(f"Sau chiến dịch: {np.mean(after_campaign):,.0f} VND")
print(f"Tăng trưởng: {((np.mean(after_campaign) - np.mean(before_campaign)) / np.mean(before_campaign) * 100):.1f}%")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Chiến dịch có hiệu quả đáng kể")
else:
    print("→ Không đủ bằng chứng về hiệu quả")
```

## 4. Paired T-Test

So sánh 2 nhóm có liên quan (cùng đối tượng, khác thời điểm).

Ví dụ:
- Trước & sau khi triển khai feature mới trên **cùng nhóm user**
- Trước & sau training trên **cùng nhân viên**

Paired t-test tận dụng việc “bắt cặp” để loại bỏ bớt biến động cá nhân.

```python
# Ví dụ: Doanh số cùng khách hàng trước và sau
customer_ids = range(30)
before = np.random.normal(1000000, 200000, 30)
after = before + np.random.normal(150000, 50000, 30)  # Tăng trung bình

# Paired t-test
t_stat, p_value = stats.ttest_rel(after, before)

print("Paired T-Test")
print(f"Before mean: {np.mean(before):,.0f}")
print(f"After mean: {np.mean(after):,.0f}")
print(f"T-statistic: {t_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Có sự thay đổi đáng kể")
else:
    print("→ Không có sự thay đổi đáng kể")
```

## 5. Chi-Square Test

Kiểm tra mối quan hệ giữa 2 biến categorical.

Ứng dụng điển hình:
- Hành vi mua hàng theo **giới tính**, **region**, **segment**
- Tỷ lệ chọn sản phẩm giữa các **channel** khác nhau

```python
# Ví dụ: Mối quan hệ giữa Region và Product preference
data = pd.DataFrame({
    "Region": ["North"] * 100 + ["South"] * 100 + ["Central"] * 100,
    "Product": (["Laptop"] * 40 + ["Mouse"] * 35 + ["Keyboard"] * 25 +
                ["Laptop"] * 30 + ["Mouse"] * 45 + ["Keyboard"] * 25 +
                ["Laptop"] * 50 + ["Mouse"] * 30 + ["Keyboard"] * 20)
})

# Tạo contingency table
contingency = pd.crosstab(data["Region"], data["Product"])
print("Contingency Table:")
print(contingency)

# Chi-square test
chi2, p_value, dof, expected = stats.chi2_contingency(contingency)

print(f"\nChi-square statistic: {chi2:.3f}")
print(f"P-value: {p_value:.4f}")
print(f"Degrees of freedom: {dof}")

if p_value < 0.05:
    print("→ Có mối quan hệ giữa Region và Product preference")
else:
    print("→ Không có mối quan hệ đáng kể")
```

**Ví dụ thực tế**: Kiểm tra độc lập
```python
# Kiểm tra xem giới tính có ảnh hưởng đến sản phẩm mua không?
purchase_data = pd.DataFrame({
    "Gender": ["Male"] * 200 + ["Female"] * 200,
    "Product": (["Laptop"] * 80 + ["Mouse"] * 70 + ["Keyboard"] * 50 +
                ["Laptop"] * 60 + ["Mouse"] * 90 + ["Keyboard"] * 50)
})

contingency = pd.crosstab(purchase_data["Gender"], purchase_data["Product"])
chi2, p_value, dof, expected = stats.chi2_contingency(contingency)

print("Kiểm tra mối quan hệ Gender - Product:")
print(contingency)
print(f"\nP-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Giới tính ảnh hưởng đến sản phẩm mua")
else:
    print("→ Giới tính không ảnh hưởng đáng kể")
```

## 6. ANOVA (Analysis of Variance)

So sánh mean của nhiều nhóm (≥ 3).

Ý tưởng:
- Thay vì test từng cặp bằng t-test (tốn công, tăng risk type I),
- ANOVA kiểm tra **tất cả nhóm cùng lúc**:
  - H0: Tất cả mean bằng nhau
  - H1: Có ít nhất một mean khác

Nếu ANOVA cho kết quả significant → dùng **post-hoc test** (Tukey HSD, Bonferroni,…) để biết **nhóm nào khác nhóm nào**.

```python
# Ví dụ: So sánh doanh số 3 khu vực
north = np.random.normal(50000000, 10000000, 50)
south = np.random.normal(55000000, 12000000, 50)
central = np.random.normal(48000000, 9000000, 50)

# One-way ANOVA
f_stat, p_value = stats.f_oneway(north, south, central)

print("One-Way ANOVA")
print(f"North mean: {np.mean(north):,.0f}")
print(f"South mean: {np.mean(south):,.0f}")
print(f"Central mean: {np.mean(central):,.0f}")
print(f"F-statistic: {f_stat:.3f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Có sự khác biệt đáng kể giữa các khu vực")
    # Post-hoc test để biết nhóm nào khác nhau
    # Lưu ý: tukey_hsd có trong scipy từ version 1.7.0+
    try:
        from scipy.stats import tukey_hsd
        result = tukey_hsd(north, south, central)
        print("\nPost-hoc test (Tukey HSD):")
        print(result)
    except ImportError:
        print("\nLưu ý: tukey_hsd cần scipy >= 1.7.0. Có thể dùng scipy.stats.tukey_hsd()")
else:
    print("→ Không có sự khác biệt đáng kể")
```

**Ví dụ thực tế**: So sánh nhiều sản phẩm
```python
# So sánh doanh số 4 sản phẩm
laptop = np.random.normal(50000000, 10000000, 30)
mouse = np.random.normal(20000000, 5000000, 30)
keyboard = np.random.normal(30000000, 8000000, 30)
monitor = np.random.normal(45000000, 9000000, 30)

f_stat, p_value = stats.f_oneway(laptop, mouse, keyboard, monitor)

print("So sánh doanh số 4 sản phẩm:")
print(f"Laptop: {np.mean(laptop):,.0f}")
print(f"Mouse: {np.mean(mouse):,.0f}")
print(f"Keyboard: {np.mean(keyboard):,.0f}")
print(f"Monitor: {np.mean(monitor):,.0f}")
print(f"P-value: {p_value:.4f}")

if p_value < 0.05:
    print("→ Có sự khác biệt đáng kể giữa các sản phẩm")
else:
    print("→ Không có sự khác biệt đáng kể")
```

## 7. Case Study: Phân Tích A/B Test

Trong thực tế, A/B test thường cần:
- Xác định **metric chính** (primary KPI): conversion, ARPU, retention,…
- Thiết kế **ngẫu nhiên hóa** (randomization) giữa control và treatment
- Chạy đủ **thời gian & sample size**
- Chỉ test **một vài giả thuyết rõ ràng**, tránh “vọc dữ liệu rồi mới nghĩ giả thuyết”

```python
# A/B Test: So sánh 2 phiên bản website
# Version A (control)
version_a_conversions = np.random.binomial(1, 0.10, 1000)  # 10% conversion
version_a_revenue = version_a_conversions * np.random.normal(1000000, 200000, 1000)

# Version B (treatment)
version_b_conversions = np.random.binomial(1, 0.12, 1000)  # 12% conversion
version_b_revenue = version_b_conversions * np.random.normal(1000000, 200000, 1000)

# Test 1: Conversion rate
from scipy.stats import chi2_contingency
contingency = [[sum(version_a_conversions), len(version_a_conversions) - sum(version_a_conversions)],
               [sum(version_b_conversions), len(version_b_conversions) - sum(version_b_conversions)]]
chi2, p_value_conv, _, _ = chi2_contingency(contingency)

# Test 2: Average revenue
t_stat, p_value_rev = stats.ttest_ind(version_b_revenue[version_b_revenue > 0], 
                                       version_a_revenue[version_a_revenue > 0])

print("A/B Test Results:")
print(f"Version A - Conversion: {sum(version_a_conversions)/len(version_a_conversions):.2%}")
print(f"Version B - Conversion: {sum(version_b_conversions)/len(version_b_conversions):.2%}")
print(f"Conversion test p-value: {p_value_conv:.4f}")

print(f"\nVersion A - Avg Revenue: {np.mean(version_a_revenue[version_a_revenue > 0]):,.0f}")
print(f"Version B - Avg Revenue: {np.mean(version_b_revenue[version_b_revenue > 0]):,.0f}")
print(f"Revenue test p-value: {p_value_rev:.4f}")

if p_value_conv < 0.05:
    print("\n→ Version B có conversion rate tốt hơn đáng kể")
if p_value_rev < 0.05:
    print("→ Version B có revenue tốt hơn đáng kể")
```

## Bài Tập Thực Hành

### Bài 1: One-Sample T-Test
```python
# Kiểm tra xem doanh số trung bình có = 50 triệu không?
# Với sample size = 100, mean = 52 triệu, std = 8 triệu
```

### Bài 2: Two-Sample T-Test
```python
# So sánh doanh số 2 chiến dịch marketing
# Campaign A: 100 samples, mean = 55 triệu
# Campaign B: 100 samples, mean = 58 triệu
```

### Bài 3: Chi-Square Test
```python
# Kiểm tra mối quan hệ giữa:
# - Customer segment (VIP, Gold, Silver)
# - Product category (Electronics, Accessories)
```

## Tổng Kết

✅ Đã học:
- Khái niệm hypothesis testing
- One-sample và two-sample t-test
- Paired t-test
- Chi-square test
- ANOVA
- P-value và significance level

**Tiếp theo**: [03. Correlation & Regression](03-correlation-regression.md)

