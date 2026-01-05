# Phân Tích Nguyên Nhân Gốc Rễ (Root Cause Analysis)

## 🎯 Mục Tiêu

Sau bài học này, bạn sẽ:
- Hiểu được tầm quan trọng của phân tích nguyên nhân gốc rễ
- Nắm vững các phương pháp phân tích nguyên nhân phổ biến
- Áp dụng được vào các tình huống thực tế trong công việc Data Analyst

## 📚 Khái Niệm

**Root Cause Analysis (RCA)** là quá trình xác định nguyên nhân cơ bản của một vấn đề, không chỉ dừng lại ở các triệu chứng bề mặt.

### Tại Sao Quan Trọng?

- **Giải quyết vấn đề tận gốc**: Thay vì chỉ xử lý triệu chứng, chúng ta tìm ra nguyên nhân thực sự
- **Tiết kiệm thời gian và tài nguyên**: Tránh lặp lại các vấn đề tương tự
- **Cải thiện quy trình**: Hiểu rõ hệ thống để tối ưu hóa

## 🔍 Các Phương Pháp Phân Tích

### 1. 5 Whys (5 Tại Sao)

**Cách thực hiện**: Liên tục hỏi "Tại sao?" cho đến khi tìm ra nguyên nhân gốc rễ.

**Ví dụ thực tế - Doanh số giảm:**

```
Vấn đề: Doanh số tháng này giảm 20%

1. Tại sao doanh số giảm?
   → Số lượng đơn hàng giảm

2. Tại sao số lượng đơn hàng giảm?
   → Tỷ lệ chuyển đổi từ visitor sang customer giảm

3. Tại sao tỷ lệ chuyển đổi giảm?
   → Thời gian tải trang tăng lên 5 giây

4. Tại sao thời gian tải trang tăng?
   → Server bị quá tải do traffic tăng đột biến

5. Tại sao server bị quá tải?
   → Không có auto-scaling, server không tự động mở rộng khi traffic tăng
```

**Nguyên nhân gốc rễ**: Thiếu hệ thống auto-scaling cho server.

**Actionable Insight**: 
- Ngắn hạn: Tăng capacity server thủ công
- Dài hạn: Triển khai auto-scaling system

### 2. Fishbone Diagram (Sơ Đồ Xương Cá)

Phân tích nguyên nhân theo các nhóm: People, Process, Technology, Environment, Materials, Management.

**Ví dụ: Tỷ lệ từ bỏ giỏ hàng cao**

```
                    Vấn đề: Cart Abandonment Rate 70%
                              |
        ┌─────────────────────┼─────────────────────┐
        |                     |                     |
    PEOPLE              PROCESS              TECHNOLOGY
        |                     |                     |
    - Thiếu training    - Checkout phức tạp   - Website chậm
    - Không hiểu UX     - Nhiều bước          - Lỗi thanh toán
    - Thiếu support     - Không có save cart  - Mobile không tối ưu
```

**Phân tích dữ liệu để xác định nguyên nhân chính:**

```python
import pandas as pd
import matplotlib.pyplot as plt

# Giả sử có dữ liệu về cart abandonment
data = {
    'reason': ['Checkout phức tạp', 'Website chậm', 'Thiếu support', 
               'Mobile không tối ưu', 'Lỗi thanh toán', 'Khác'],
    'percentage': [35, 25, 15, 12, 8, 5]
}

df = pd.DataFrame(data)
df = df.sort_values('percentage', ascending=False)

# Visualize
plt.figure(figsize=(10, 6))
plt.barh(df['reason'], df['percentage'])
plt.xlabel('Tỷ lệ (%)')
plt.title('Nguyên Nhân Cart Abandonment')
plt.show()

# Kết luận: Checkout phức tạp là nguyên nhân chính (35%)
```

### 3. Pareto Analysis (Phân Tích 80/20)

**Nguyên lý**: 80% vấn đề đến từ 20% nguyên nhân.

**Ví dụ: Phân tích lỗi sản phẩm**

```python
import pandas as pd
import numpy as np

# Dữ liệu lỗi sản phẩm
errors = {
    'loi_loai': ['Lỗi đóng gói', 'Lỗi vận chuyển', 'Lỗi sản xuất', 
                 'Lỗi chất lượng', 'Lỗi đặt hàng', 'Lỗi khác'],
    'so_luong': [450, 320, 180, 120, 80, 50]
}

df = pd.DataFrame(errors)
df = df.sort_values('so_luong', ascending=False)
df['cumulative_pct'] = (df['so_luong'].cumsum() / df['so_luong'].sum() * 100).round(2)

print(df)

# Tập trung vào 2 lỗi đầu tiên chiếm > 50% tổng lỗi
# → Ưu tiên fix: Lỗi đóng gói và Lỗi vận chuyển
```

## 📊 Case Study: Phân Tích Drop-off Rate

### Tình Huống

Một ứng dụng e-learning có tỷ lệ người dùng bỏ học sau tuần đầu tăng từ 15% lên 35%.

### Bước 1: Thu Thập Dữ Liệu

```python
import pandas as pd

# Dữ liệu user behavior
user_data = pd.read_csv('user_behavior.csv')

# Phân tích theo cohort
cohort_analysis = user_data.groupby(['cohort_week', 'week_number']).agg({
    'user_id': 'count',
    'lessons_completed': 'mean',
    'time_spent_minutes': 'mean'
}).reset_index()
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
}, index=['Lessons Completed', 'Time Spent', 'Quiz Score', 'Support Tickets'])

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
    'completion_rate': 'mean'
}).reset_index()

# Visualize
import matplotlib.pyplot as plt

plt.scatter(difficulty_retention['lesson_difficulty'], 
            difficulty_retention['retained'])
plt.xlabel('Độ Khó Bài Học')
plt.ylabel('Tỷ Lệ Giữ Lại')
plt.title('Mối Quan Hệ Giữa Độ Khó và Retention')
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

## 🔗 Tài Liệu Tham Khảo

- Root Cause Analysis: A Step-by-Step Guide
- The 5 Whys Technique
- Pareto Principle in Data Analysis

---

**Lưu ý**: Phân tích nguyên nhân gốc rễ là kỹ năng cốt lõi của Data Analyst. Thực hành thường xuyên với các case study thực tế sẽ giúp bạn phát triển tư duy phân tích sâu sắc.

