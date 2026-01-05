# 03. Automation & Scheduling

## Mục Tiêu
- Tự động hóa các tác vụ phân tích
- Tạo scheduled tasks
- Gửi email báo cáo tự động
- Tạo scripts có thể chạy định kỳ

## Tại Sao Cần Automation?

- **Tiết kiệm thời gian**: Tự động hóa các công việc lặp lại
- **Giảm lỗi**: Tránh lỗi do thao tác thủ công
- **Báo cáo định kỳ**: Tự động tạo và gửi báo cáo
- **Real-time monitoring**: Theo dõi dữ liệu liên tục

## 1. Tạo Script Tự Động

### Script Cơ Bản

```python
import pandas as pd
import numpy as np
from datetime import datetime
import os

def generate_daily_report():
    """Tạo báo cáo hàng ngày"""
    print(f"Bắt đầu tạo báo cáo: {datetime.now()}")
    
    # 1. Đọc dữ liệu
    # Giả sử có file CSV
    # df = pd.read_csv("sales_data.csv")
    
    # Mock data
    df = pd.DataFrame({
        "Date": pd.date_range("2024-01-01", periods=30, freq="D"),
        "Sales": np.random.randint(10000000, 50000000, 30),
        "Product": np.random.choice(["Laptop", "Mouse", "Keyboard"], 30)
    })
    
    # 2. Phân tích
    summary = df.groupby("Product").agg({
        "Sales": ["sum", "mean", "count"]
    })
    
    # 3. Lưu kết quả
    output_dir = "reports"
    os.makedirs(output_dir, exist_ok=True)
    
    today = datetime.now().strftime("%Y%m%d")
    output_file = f"{output_dir}/daily_report_{today}.csv"
    summary.to_csv(output_file)
    
    print(f"Báo cáo đã được lưu: {output_file}")
    return summary

# Chạy script
if __name__ == "__main__":
    report = generate_daily_report()
    print("\nTóm tắt báo cáo:")
    print(report)
```

## 2. Gửi Email Tự Động

### Cài Đặt

```bash
pip install smtplib email
```

### Gửi Email Đơn Giản

```python
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email import encoders
import os

def send_email_report(subject, body, attachment_path=None, 
                      to_email="recipient@example.com",
                      from_email="sender@example.com",
                      password="your_password"):
    """Gửi email với báo cáo"""
    
    # Tạo message
    msg = MIMEMultipart()
    msg["From"] = from_email
    msg["To"] = to_email
    msg["Subject"] = subject
    
    # Body
    msg.attach(MIMEText(body, "plain"))
    
    # Attachment
    if attachment_path and os.path.exists(attachment_path):
        with open(attachment_path, "rb") as attachment:
            part = MIMEBase("application", "octet-stream")
            part.set_payload(attachment.read())
        
        encoders.encode_base64(part)
        part.add_header(
            "Content-Disposition",
            f"attachment; filename= {os.path.basename(attachment_path)}"
        )
        msg.attach(part)
    
    # Gửi email
    try:
        server = smtplib.SMTP("smtp.gmail.com", 587)
        server.starttls()
        server.login(from_email, password)
        text = msg.as_string()
        server.sendmail(from_email, to_email, text)
        server.quit()
        print("Email đã được gửi thành công!")
    except Exception as e:
        print(f"Lỗi gửi email: {e}")

# Sử dụng
# send_email_report(
#     subject="Báo Cáo Doanh Số Hàng Ngày",
#     body="Xin chào,\n\nĐính kèm báo cáo doanh số hôm nay.",
#     attachment_path="reports/daily_report_20240115.csv"
# )
```

**Lưu ý**: Với Gmail, cần sử dụng App Password thay vì mật khẩu thông thường.

## 3. Scheduling với Schedule Library

### Cài Đặt

```bash
pip install schedule
```

### Lên Lịch Tác Vụ

```python
import schedule
import time

def daily_report():
    """Chạy báo cáo hàng ngày"""
    print(f"Chạy báo cáo: {datetime.now()}")
    generate_daily_report()

# Lên lịch
schedule.every().day.at("09:00").do(daily_report)
schedule.every().monday.at("08:00").do(daily_report)  # Thứ 2
schedule.every().hour.do(daily_report)  # Mỗi giờ

# Chạy
while True:
    schedule.run_pending()
    time.sleep(60)  # Kiểm tra mỗi phút
```

## 4. Windows Task Scheduler

### Tạo Batch File

Tạo file `run_report.bat`:

```batch
@echo off
cd /d D:\Data Analyst
python daily_report.py
pause
```

### Tạo Python Script

```python
# daily_report.py
import pandas as pd
import sys
import os

# Thêm path để import modules
sys.path.append(os.path.dirname(__file__))

def main():
    try:
        # Code tạo báo cáo
        print("Đang tạo báo cáo...")
        # ... your code here ...
        print("Hoàn thành!")
    except Exception as e:
        print(f"Lỗi: {e}")
        sys.exit(1)

if __name__ == "__main__":
    main()
```

Sau đó tạo task trong Windows Task Scheduler để chạy `run_report.bat`.

## 5. Cron Jobs (Linux/Mac)

### Tạo Cron Job

```bash
# Mở crontab
crontab -e

# Thêm dòng (chạy mỗi ngày lúc 9:00 AM)
0 9 * * * /usr/bin/python3 /path/to/daily_report.py

# Hoặc với log
0 9 * * * /usr/bin/python3 /path/to/daily_report.py >> /path/to/log.txt 2>&1
```

### Cron Syntax

```
* * * * *
│ │ │ │ │
│ │ │ │ └─── Day of week (0-7, 0 và 7 = Sunday)
│ │ │ └───── Month (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour (0-23)
└─────────── Minute (0-59)
```

## 6. Case Study: Hệ Thống Báo Cáo Tự Động

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta
import os
import logging

# Setup logging
logging.basicConfig(
    filename="report_automation.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

class ReportAutomation:
    def __init__(self, data_source="sales_data.csv"):
        self.data_source = data_source
        self.output_dir = "reports"
        os.makedirs(self.output_dir, exist_ok=True)
    
    def load_data(self):
        """Load dữ liệu"""
        try:
            # Giả sử có file CSV
            # df = pd.read_csv(self.data_source)
            
            # Mock data
            df = pd.DataFrame({
                "Date": pd.date_range("2024-01-01", periods=30, freq="D"),
                "Product": np.random.choice(["Laptop", "Mouse", "Keyboard"], 30),
                "Sales": np.random.randint(10000000, 50000000, 30),
                "Region": np.random.choice(["North", "South", "Central"], 30)
            })
            
            logging.info(f"Loaded {len(df)} records")
            return df
        except Exception as e:
            logging.error(f"Error loading data: {e}")
            return None
    
    def generate_summary(self, df):
        """Tạo tổng hợp"""
        summary = {
            "total_sales": df["Sales"].sum(),
            "avg_sales": df["Sales"].mean(),
            "product_summary": df.groupby("Product")["Sales"].sum().to_dict(),
            "region_summary": df.groupby("Region")["Sales"].sum().to_dict(),
            "date_range": {
                "start": df["Date"].min().strftime("%Y-%m-%d"),
                "end": df["Date"].max().strftime("%Y-%m-%d")
            }
        }
        return summary
    
    def create_report(self):
        """Tạo báo cáo"""
        logging.info("Starting report generation")
        
        # Load data
        df = self.load_data()
        if df is None:
            return None
        
        # Generate summary
        summary = self.generate_summary(df)
        
        # Save to CSV
        today = datetime.now().strftime("%Y%m%d")
        output_file = f"{self.output_dir}/report_{today}.csv"
        
        summary_df = pd.DataFrame([summary])
        summary_df.to_csv(output_file, index=False)
        
        logging.info(f"Report saved: {output_file}")
        
        # Save detailed report
        detail_file = f"{self.output_dir}/detail_{today}.csv"
        df.to_csv(detail_file, index=False)
        
        return summary
    
    def send_notification(self, summary):
        """Gửi thông báo (có thể là email, Slack, etc.)"""
        if summary:
            message = f"""
Báo Cáo Doanh Số - {datetime.now().strftime("%Y-%m-%d")}

Tổng doanh số: {summary['total_sales']:,.0f} VND
Doanh số trung bình: {summary['avg_sales']:,.0f} VND

Top sản phẩm:
{chr(10).join([f"  {k}: {v:,.0f} VND" for k, v in summary['product_summary'].items()])}
"""
            print(message)
            logging.info("Notification sent")

# Sử dụng
if __name__ == "__main__":
    automation = ReportAutomation()
    summary = automation.create_report()
    automation.send_notification(summary)
```

## 7. Error Handling và Logging

```python
import logging
from datetime import datetime

# Setup logging
logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    handlers=[
        logging.FileHandler("automation.log"),
        logging.StreamHandler()
    ]
)

def safe_execute(func, *args, **kwargs):
    """Wrapper để xử lý lỗi"""
    try:
        result = func(*args, **kwargs)
        logging.info(f"{func.__name__} completed successfully")
        return result
    except Exception as e:
        logging.error(f"Error in {func.__name__}: {e}", exc_info=True)
        return None

# Sử dụng
result = safe_execute(generate_daily_report)
```

## 8. Best Practices

### 1. Configuration File

```python
# config.py
CONFIG = {
    "data_source": "sales_data.csv",
    "output_dir": "reports",
    "email": {
        "smtp_server": "smtp.gmail.com",
        "smtp_port": 587,
        "from_email": "sender@example.com",
        "to_email": "recipient@example.com"
    },
    "schedule": {
        "daily_report_time": "09:00",
        "weekly_report_day": "monday"
    }
}
```

### 2. Environment Variables

```python
import os
from dotenv import load_dotenv

load_dotenv()

EMAIL_PASSWORD = os.getenv("EMAIL_PASSWORD")
DATABASE_URL = os.getenv("DATABASE_URL")
```

### 3. Testing

```python
def test_report_generation():
    """Test report generation"""
    automation = ReportAutomation()
    summary = automation.create_report()
    assert summary is not None
    assert "total_sales" in summary
    print("Test passed!")

if __name__ == "__main__":
    test_report_generation()
```

## Bài Tập Thực Hành

### Bài 1: Tạo Script Tự Động
```python
# Tạo script:
# 1. Đọc dữ liệu từ CSV
# 2. Tính tổng hợp
# 3. Lưu báo cáo
# 4. Gửi email (optional)
```

### Bài 2: Scheduling
```python
# Tạo scheduled task:
# - Chạy báo cáo hàng ngày lúc 9:00 AM
# - Gửi email báo cáo tuần vào thứ 2
```

### Bài 3: Error Handling
```python
# Thêm error handling và logging:
# - Log tất cả errors
# - Gửi thông báo khi có lỗi
# - Retry logic cho failed tasks
```

## Tổng Kết

✅ Đã học:
- Tạo scripts tự động
- Gửi email tự động
- Scheduling với schedule library
- Windows Task Scheduler và Cron
- Error handling và logging
- Best practices

**Tiếp theo**: [04. Machine Learning Basics](04-machine-learning-basics.md)

