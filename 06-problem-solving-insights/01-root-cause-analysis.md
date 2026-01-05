# Phân Tích Nguyên Nhân Gốc Rễ (Root Cause Analysis)

## 🎯 Mục Tiêu

Sau bài học này, bạn sẽ:
- Hiểu được tầm quan trọng của phân tích nguyên nhân gốc rễ
- Nắm vững các phương pháp phân tích nguyên nhân phổ biến
- Áp dụng được vào các tình huống thực tế trong công việc Data Analyst
- Biết cách chuyển từ triệu chứng sang nguyên nhân thực sự

## 📚 Khái Niệm Cơ Bản

### Root Cause Analysis (RCA) Là Gì?

**Root Cause Analysis (RCA)** là quá trình xác định nguyên nhân cơ bản của một vấn đề, không chỉ dừng lại ở các triệu chứng bề mặt.

**Tại sao quan trọng?**

- **Giải quyết vấn đề tận gốc**: Thay vì chỉ xử lý triệu chứng, chúng ta tìm ra nguyên nhân thực sự
- **Tiết kiệm thời gian và tài nguyên**: Tránh lặp lại các vấn đề tương tự
- **Cải thiện quy trình**: Hiểu rõ hệ thống để tối ưu hóa
- **Ra quyết định đúng đắn**: Dựa trên dữ liệu và phân tích, không phải đoán mò

### Phân Biệt: Triệu Chứng vs Nguyên Nhân

**Triệu chứng (Symptom)**: Dấu hiệu bề mặt của vấn đề
- Ví dụ: "Doanh số giảm 20%"
- Ví dụ: "Tỷ lệ churn tăng"
- Ví dụ: "Customer complaints tăng"

**Nguyên nhân gốc rễ (Root Cause)**: Lý do thực sự gây ra vấn đề
- Ví dụ: "Server không có auto-scaling → quá tải khi traffic tăng"
- Ví dụ: "Onboarding quá phức tạp → users bỏ cuộc"
- Ví dụ: "Thiếu training cho support team → xử lý chậm"

## 🔍 Các Phương Pháp Phân Tích

### 1. 5 Whys (5 Tại Sao)

**Cách thực hiện**: Liên tục hỏi "Tại sao?" cho đến khi tìm ra nguyên nhân gốc rễ (thường 5 lần).

**Quy tắc**:
- Hỏi "Tại sao?" ít nhất 5 lần
- Mỗi câu trả lời phải dựa trên dữ liệu, không phải đoán
- Dừng lại khi không thể hỏi "Tại sao?" nữa hoặc đã tìm ra nguyên nhân có thể hành động

**Ví dụ thực tế - Doanh số giảm:**

```
Vấn đề: Doanh số tháng này giảm 20%

1. Tại sao doanh số giảm?
   → Số lượng đơn hàng giảm (từ 1000 → 800 đơn)

2. Tại sao số lượng đơn hàng giảm?
   → Tỷ lệ chuyển đổi từ visitor sang customer giảm (từ 5% → 3%)

3. Tại sao tỷ lệ chuyển đổi giảm?
   → Thời gian tải trang tăng lên 5 giây (từ 2 giây)

4. Tại sao thời gian tải trang tăng?
   → Server bị quá tải do traffic tăng đột biến (từ 10k → 50k users/ngày)

5. Tại sao server bị quá tải?
   → Không có auto-scaling, server không tự động mở rộng khi traffic tăng
```

**Nguyên nhân gốc rễ**: Thiếu hệ thống auto-scaling cho server.

**Actionable Insight**: 
- **Ngắn hạn**: Tăng capacity server thủ công, tối ưu code
- **Dài hạn**: Triển khai auto-scaling system

**Ví dụ với Python - Phân tích dữ liệu:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Dữ liệu doanh số theo ngày
dates = pd.date_range('2024-01-01', '2024-01-31', freq='D')
sales_data = pd.DataFrame({
    'date': dates,
    'orders': np.random.randint(80, 120, 31),  # Giảm từ 100 xuống 80-120
    'visitors': np.random.randint(2000, 3000, 31),
    'page_load_time': np.random.uniform(4.5, 5.5, 31),  # Tăng lên 5s
    'server_cpu': np.random.uniform(85, 95, 31)  # CPU cao
})

# Tính conversion rate
sales_data['conversion_rate'] = (sales_data['orders'] / sales_data['visitors']) * 100

# Phân tích correlation
print("=== PHÂN TÍCH NGUYÊN NHÂN ===")
print(f"Conversion rate trung bình: {sales_data['conversion_rate'].mean():.2f}%")
print(f"Page load time trung bình: {sales_data['page_load_time'].mean():.2f}s")
print(f"Server CPU trung bình: {sales_data['server_cpu'].mean():.1f}%")

# Correlation analysis
corr = sales_data[['orders', 'conversion_rate', 'page_load_time', 'server_cpu']].corr()
print("\nCorrelation Matrix:")
print(corr)

# Visualize
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

axes[0, 0].plot(sales_data['date'], sales_data['orders'], marker='o')
axes[0, 0].set_title('Số Lượng Đơn Hàng')
axes[0, 0].set_ylabel('Orders')
axes[0, 0].tick_params(axis='x', rotation=45)

axes[0, 1].plot(sales_data['date'], sales_data['conversion_rate'], marker='o', color='orange')
axes[0, 1].set_title('Conversion Rate')
axes[0, 1].set_ylabel('%')
axes[0, 1].tick_params(axis='x', rotation=45)

axes[1, 0].plot(sales_data['date'], sales_data['page_load_time'], marker='o', color='red')
axes[1, 0].set_title('Page Load Time')
axes[1, 0].set_ylabel('Seconds')
axes[1, 0].tick_params(axis='x', rotation=45)

axes[1, 1].plot(sales_data['date'], sales_data['server_cpu'], marker='o', color='purple')
axes[1, 1].set_title('Server CPU Usage')
axes[1, 1].set_ylabel('%')
axes[1, 1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.show()

# Kết luận
print("\n=== KẾT LUẬN ===")
if corr.loc['orders', 'page_load_time'] < -0.5:
    print("→ Page load time có tương quan âm mạnh với orders")
    print("→ Nguyên nhân có thể: Server chậm → User experience kém → Conversion giảm")
```

### 2. Fishbone Diagram (Sơ Đồ Xương Cá / Ishikawa)

Phân tích nguyên nhân theo các nhóm: **People, Process, Technology, Environment, Materials, Management**.

**Cấu trúc:**

```
                    Vấn đề: [Mô tả vấn đề]
                              |
        ┌─────────────────────┼─────────────────────┐
        |                     |                     |
    PEOPLE              PROCESS              TECHNOLOGY
        |                     |                     |
    - Thiếu training    - Checkout phức tạp   - Website chậm
    - Không hiểu UX     - Nhiều bước          - Lỗi thanh toán
    - Thiếu support     - Không có save cart  - Mobile không tối ưu
        |                     |                     |
    ────────────────────────────────────────────────────
        |                     |                     |
    ENVIRONMENT         MATERIALS            MANAGEMENT
        |                     |                     |
    - Thời tiết         - Hàng hết            - Thiếu budget
    - Mùa vụ             - Chất lượng kém      - Thiếu strategy
    - Đối thủ            - Giá cao             - Thiếu monitoring
```

**Ví dụ: Tỷ lệ từ bỏ giỏ hàng cao (Cart Abandonment Rate 70%)**

```python
import pandas as pd
import matplotlib.pyplot as plt

# Dữ liệu về cart abandonment
abandonment_data = {
    'category': ['People', 'Process', 'Technology', 'Environment', 'Materials', 'Management'],
    'issues': [
        ['Thiếu training', 'Không hiểu UX', 'Thiếu support'],
        ['Checkout phức tạp', 'Nhiều bước', 'Không có save cart'],
        ['Website chậm', 'Lỗi thanh toán', 'Mobile không tối ưu'],
        ['Thời tiết', 'Mùa vụ', 'Đối thủ'],
        ['Hàng hết', 'Chất lượng kém', 'Giá cao'],
        ['Thiếu budget', 'Thiếu strategy', 'Thiếu monitoring']
    ]
}

# Dữ liệu thực tế từ survey/analytics
survey_data = {
    'reason': ['Checkout phức tạp', 'Website chậm', 'Thiếu support', 
               'Mobile không tối ưu', 'Lỗi thanh toán', 'Giá cao', 'Khác'],
    'percentage': [35, 25, 15, 12, 8, 3, 2]
}

df = pd.DataFrame(survey_data)
df = df.sort_values('percentage', ascending=False)

# Visualize
plt.figure(figsize=(10, 6))
bars = plt.barh(df['reason'], df['percentage'], color='steelblue')
plt.xlabel('Tỷ lệ (%)', fontsize=12)
plt.title('Nguyên Nhân Cart Abandonment', fontsize=16, fontweight='bold')
plt.grid(axis='x', alpha=0.3)

# Thêm giá trị trên mỗi bar
for i, (idx, row) in enumerate(df.iterrows()):
    plt.text(row['percentage'] + 1, i, f"{row['percentage']}%", 
             va='center', fontsize=10)

plt.tight_layout()
plt.show()

# Phân loại theo Fishbone
people_issues = ['Thiếu support']
process_issues = ['Checkout phức tạp']
tech_issues = ['Website chậm', 'Mobile không tối ưu', 'Lỗi thanh toán']
material_issues = ['Giá cao']

print("\n=== PHÂN TÍCH THEO FISHBONE ===")
print(f"People: {sum(df[df['reason'].isin(people_issues)]['percentage'])}%")
print(f"Process: {sum(df[df['reason'].isin(process_issues)]['percentage'])}%")
print(f"Technology: {sum(df[df['reason'].isin(tech_issues)]['percentage'])}%")
print(f"Materials: {sum(df[df['reason'].isin(material_issues)]['percentage'])}%")

# Kết luận
print("\n→ Nguyên nhân chính: Process (Checkout phức tạp) và Technology (Website chậm)")
print("→ Ưu tiên: Đơn giản hóa checkout và tối ưu tốc độ website")
```

### 3. Pareto Analysis (Phân Tích 80/20)

**Nguyên lý Pareto**: 80% vấn đề đến từ 20% nguyên nhân.

**Cách thực hiện**:
1. Liệt kê tất cả nguyên nhân có thể
2. Đo lường tần suất/tác động của từng nguyên nhân
3. Sắp xếp theo thứ tự giảm dần
4. Xác định 20% nguyên nhân gây ra 80% vấn đề

**Ví dụ: Phân tích lỗi sản phẩm**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Dữ liệu lỗi sản phẩm
errors = {
    'loi_loai': ['Lỗi đóng gói', 'Lỗi vận chuyển', 'Lỗi sản xuất', 
                 'Lỗi chất lượng', 'Lỗi đặt hàng', 'Lỗi khác'],
    'so_luong': [450, 320, 180, 120, 80, 50]
}

df = pd.DataFrame(errors)
df = df.sort_values('so_luong', ascending=False)
df['cumulative'] = df['so_luong'].cumsum()
df['cumulative_pct'] = (df['cumulative'] / df['so_luong'].sum() * 100).round(2)
df['pct'] = (df['so_luong'] / df['so_luong'].sum() * 100).round(2)

print("=== PARETO ANALYSIS ===")
print(df[['loi_loai', 'so_luong', 'pct', 'cumulative_pct']])

# Visualize Pareto Chart
fig, ax1 = plt.subplots(figsize=(12, 6))

# Bar chart
bars = ax1.bar(df['loi_loai'], df['so_luong'], color='steelblue', alpha=0.7)
ax1.set_xlabel('Loại Lỗi', fontsize=12)
ax1.set_ylabel('Số Lượng', fontsize=12, color='steelblue')
ax1.tick_params(axis='y', labelcolor='steelblue')
ax1.tick_params(axis='x', rotation=45)

# Line chart (cumulative percentage)
ax2 = ax1.twinx()
ax2.plot(df['loi_loai'], df['cumulative_pct'], color='red', marker='o', linewidth=2)
ax2.axhline(y=80, color='red', linestyle='--', alpha=0.5, label='80%')
ax2.set_ylabel('Cumulative %', fontsize=12, color='red')
ax2.tick_params(axis='y', labelcolor='red')

plt.title('Pareto Chart - Phân Tích Lỗi Sản Phẩm', fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()

# Xác định 20% nguyên nhân gây ra 80% vấn đề
top_20_pct = df[df['cumulative_pct'] <= 80]
print(f"\n=== TOP 20% NGUYÊN NHÂN (Gây ra 80% vấn đề) ===")
print(top_20_pct[['loi_loai', 'so_luong', 'cumulative_pct']])

print("\n→ Ưu tiên fix: Lỗi đóng gói và Lỗi vận chuyển")
```

## 📊 Case Study: Phân Tích Drop-off Rate

### Tình Huống

Một ứng dụng e-learning có tỷ lệ người dùng bỏ học sau tuần đầu tăng từ 15% lên 35%.

### Bước 1: Thu Thập Dữ Liệu

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Tạo dữ liệu mẫu về user behavior
np.random.seed(42)
user_data = pd.DataFrame({
    'user_id': range(1, 1001),
    'cohort_week': np.random.choice([1, 2, 3, 4], 1000),
    'lessons_completed': np.random.poisson(5, 1000),
    'time_spent_minutes': np.random.normal(45, 15, 1000),
    'quiz_score': np.random.normal(75, 10, 1000),
    'support_tickets': np.random.poisson(1, 1000),
    'lesson_difficulty': np.random.choice([1, 2, 3, 4, 5], 1000, p=[0.1, 0.2, 0.3, 0.25, 0.15]),
    'retained': np.random.choice([True, False], 1000, p=[0.65, 0.35])  # 35% churn
})

# Phân tích theo cohort
cohort_analysis = user_data.groupby('cohort_week').agg({
    'user_id': 'count',
    'lessons_completed': 'mean',
    'time_spent_minutes': 'mean',
    'retained': 'mean'
}).reset_index()

print("=== COHORT ANALYSIS ===")
print(cohort_analysis)
```

### Bước 2: Phân Tích Nguyên Nhân

```python
# So sánh giữa users giữ lại vs bỏ học
retained = user_data[user_data['retained'] == True]
churned = user_data[user_data['retained'] == False]

# So sánh metrics
comparison = pd.DataFrame({
    'retained': [
        retained['lessons_completed'].mean(),
        retained['time_spent_minutes'].mean(),
        retained['quiz_score'].mean(),
        retained['support_tickets'].mean()
    ],
    'churned': [
        churned['lessons_completed'].mean(),
        churned['time_spent_minutes'].mean(),
        churned['quiz_score'].mean(),
        churned['support_tickets'].mean()
    ]
}, index=['Lessons Completed', 'Time Spent (min)', 'Quiz Score', 'Support Tickets'])

comparison['difference'] = comparison['retained'] - comparison['churned']
comparison['difference_pct'] = (comparison['difference'] / comparison['churned'] * 100).round(2)

print("\n=== SO SÁNH RETAINED vs CHURNED ===")
print(comparison)
```

### Bước 3: Áp Dụng 5 Whys

```
1. Tại sao drop-off rate tăng?
   → Nhiều users bỏ học sau tuần đầu

2. Tại sao users bỏ học sau tuần đầu?
   → Họ không hoàn thành bài học tuần đầu

3. Tại sao họ không hoàn thành bài học tuần đầu?
   → Thời gian học trung bình giảm từ 45 phút xuống 15 phút

4. Tại sao thời gian học giảm?
   → Nội dung bài học tuần đầu quá khó, users bỏ cuộc

5. Tại sao nội dung quá khó?
   → Có thay đổi curriculum tuần đầu, tăng độ khó mà không có onboarding
```

### Bước 4: Xác Nhận với Dữ Liệu

```python
# Phân tích correlation giữa độ khó và retention
difficulty_retention = user_data.groupby('lesson_difficulty').agg({
    'retained': 'mean',
    'lessons_completed': 'mean',
    'time_spent_minutes': 'mean'
}).reset_index()

print("\n=== PHÂN TÍCH THEO ĐỘ KHÓ ===")
print(difficulty_retention)

# Visualize
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

axes[0].scatter(difficulty_retention['lesson_difficulty'], 
                difficulty_retention['retained'] * 100,
                s=100, alpha=0.7)
axes[0].set_xlabel('Độ Khó Bài Học')
axes[0].set_ylabel('Tỷ Lệ Giữ Lại (%)')
axes[0].set_title('Mối Quan Hệ Giữa Độ Khó và Retention')
axes[0].grid(True, alpha=0.3)

axes[1].bar(difficulty_retention['lesson_difficulty'], 
            difficulty_retention['time_spent_minutes'],
            color='orange', alpha=0.7)
axes[1].set_xlabel('Độ Khó Bài Học')
axes[1].set_ylabel('Thời Gian Học (phút)')
axes[1].set_title('Thời Gian Học Theo Độ Khó')
axes[1].grid(True, alpha=0.3, axis='y')

plt.tight_layout()
plt.show()
```

### Bước 5: Actionable Insights

**Nguyên nhân gốc rễ**: Curriculum tuần đầu quá khó, thiếu onboarding phù hợp.

**Giải pháp**:
1. **Ngắn hạn**: Thêm tutorial và hướng dẫn cho bài học tuần đầu
2. **Dài hạn**: Điều chỉnh lại curriculum, tăng độ khó từ từ
3. **Monitoring**: Theo dõi completion rate và time spent theo từng lesson

## 🛠️ Framework Thực Hành

### Checklist Phân Tích Nguyên Nhân

- [ ] Xác định vấn đề rõ ràng (có số liệu cụ thể)
- [ ] Thu thập đủ dữ liệu liên quan
- [ ] Áp dụng ít nhất 1 phương pháp phân tích (5 Whys, Fishbone, Pareto)
- [ ] Xác nhận nguyên nhân bằng dữ liệu
- [ ] Xác định nguyên nhân gốc rễ (không phải triệu chứng)
- [ ] Đưa ra giải pháp actionable

### Khi Nào Dùng Phương Pháp Nào?

- **5 Whys**: Khi có một vấn đề cụ thể, cần tìm nguyên nhân sâu
- **Fishbone**: Khi cần phân tích toàn diện, nhiều khía cạnh
- **Pareto**: Khi có nhiều nguyên nhân, cần ưu tiên

## 📝 Bài Tập Thực Hành

### Bài Tập 1: Phân Tích Tỷ Lệ Hoàn Trả Hàng

**Tình huống**: Tỷ lệ hoàn trả hàng tăng từ 5% lên 12% trong quý vừa qua.

**Yêu cầu**:
1. Áp dụng phương pháp 5 Whys để tìm nguyên nhân
2. Sử dụng Python để phân tích dữ liệu hoàn trả
3. Xác định top 3 nguyên nhân chính
4. Đưa ra actionable insights

### Bài Tập 2: Phân Tích Churn Rate

**Tình huống**: Churn rate của subscription service tăng 8% trong tháng.

**Yêu cầu**:
1. Tạo Fishbone diagram với các nhóm: Product, Pricing, Support, Competition
2. Phân tích dữ liệu để xác định nguyên nhân chính
3. So sánh metrics giữa churned và retained users
4. Đề xuất giải pháp cụ thể

## 💡 Tips & Best Practices

1. **Luôn bắt đầu với dữ liệu**: Đừng đoán, hãy phân tích
2. **Hỏi "Tại sao?" nhiều lần**: Đừng dừng lại ở nguyên nhân đầu tiên
3. **Sử dụng visualization**: Charts giúp nhìn thấy patterns dễ hơn
4. **Xác nhận với stakeholders**: Đảm bảo bạn hiểu đúng business context
5. **Document quá trình**: Ghi lại để học hỏi và tránh lặp lại
6. **Tránh "blame game"**: Tập trung vào hệ thống/quy trình, không phải cá nhân

## 🔗 Tài Liệu Tham Khảo

- Root Cause Analysis: A Step-by-Step Guide
- The 5 Whys Technique
- Pareto Principle in Data Analysis
- Fishbone Diagram (Ishikawa Diagram)

---

**Lưu ý**: Phân tích nguyên nhân gốc rễ là kỹ năng cốt lõi của Data Analyst. Thực hành thường xuyên với các case study thực tế sẽ giúp bạn phát triển tư duy phân tích sâu sắc.

