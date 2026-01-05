# 02. Working with Databases

## Mục Tiêu
- Kết nối với databases (SQLite, PostgreSQL, MySQL)
- Đọc và ghi dữ liệu từ databases
- Sử dụng SQLAlchemy
- Tối ưu queries

## Tại Sao Cần Databases?

Data Analyst thường làm việc với:
- **Large datasets**: Dữ liệu lớn không thể fit trong memory
- **Multiple sources**: Nhiều nguồn dữ liệu cần kết hợp
- **Real-time data**: Dữ liệu được cập nhật liên tục
- **Data warehouse**: Dữ liệu tập trung

## 1. SQLite (Database Nhẹ)

### Kết Nối và Tạo Database

```python
import sqlite3
import pandas as pd

# Tạo/kết nối database
conn = sqlite3.connect("sales.db")
cursor = conn.cursor()

# Tạo table
cursor.execute("""
CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price REAL,
    stock INTEGER
)
""")

# Insert data
products = [
    ("Laptop", 15000000, 50),
    ("Mouse", 500000, 200),
    ("Keyboard", 2000000, 100)
]

cursor.executemany("INSERT INTO products (name, price, stock) VALUES (?, ?, ?)", products)
conn.commit()
```

### Đọc Dữ Liệu với Pandas

```python
# Đọc từ SQL
query = "SELECT * FROM products"
df = pd.read_sql_query(query, conn)
print(df)

# Đọc với điều kiện
query = "SELECT * FROM products WHERE price > 1000000"
df_expensive = pd.read_sql_query(query, conn)
print(df_expensive)

# Đóng connection
conn.close()
```

### Ghi Dữ Liệu với Pandas

```python
# Tạo DataFrame
new_products = pd.DataFrame({
    "name": ["Monitor", "Speaker"],
    "price": [5000000, 3000000],
    "stock": [80, 60]
})

# Ghi vào database
conn = sqlite3.connect("sales.db")
new_products.to_sql("products", conn, if_exists="append", index=False)
conn.close()
```

## 2. SQLAlchemy (ORM)

SQLAlchemy giúp làm việc với databases dễ dàng hơn.

### Cài Đặt

```bash
pip install sqlalchemy
```

### Kết Nối

```python
from sqlalchemy import create_engine

# SQLite
engine = create_engine("sqlite:///sales.db")

# PostgreSQL
# engine = create_engine("postgresql://user:password@localhost/dbname")

# MySQL
# engine = create_engine("mysql+pymysql://user:password@localhost/dbname")
```

### Đọc và Ghi với Pandas

```python
# Đọc
df = pd.read_sql("SELECT * FROM products", engine)
print(df)

# Ghi
df_new = pd.DataFrame({
    "name": ["Tablet", "Phone"],
    "price": [8000000, 12000000],
    "stock": [40, 30]
})

df_new.to_sql("products", engine, if_exists="append", index=False)
```

## 3. PostgreSQL

### Kết Nối

```python
# Cài đặt: pip install psycopg2
from sqlalchemy import create_engine

# Connection string
engine = create_engine(
    "postgresql://username:password@localhost:5432/database_name"
)

# Test connection
try:
    conn = engine.connect()
    print("Connected successfully!")
    conn.close()
except Exception as e:
    print(f"Connection failed: {e}")
```

### Đọc Dữ Liệu

```python
# Đọc table
df = pd.read_sql_table("products", engine)
print(df)

# Đọc với query
query = """
SELECT 
    category,
    AVG(price) as avg_price,
    SUM(stock) as total_stock
FROM products
GROUP BY category
"""
df_summary = pd.read_sql(query, engine)
print(df_summary)
```

## 4. MySQL

### Kết Nối

```python
# Cài đặt: pip install pymysql
from sqlalchemy import create_engine

engine = create_engine(
    "mysql+pymysql://username:password@localhost:3306/database_name"
)

# Đọc dữ liệu
df = pd.read_sql("SELECT * FROM products", engine)
```

## 5. Complex Queries

### JOIN Queries

```python
# Tạo dữ liệu mẫu
conn = sqlite3.connect("sales.db")
cursor = conn.cursor()

# Tạo tables
cursor.execute("""
CREATE TABLE IF NOT EXISTS orders (
    id INTEGER PRIMARY KEY,
    customer_id INTEGER,
    product_id INTEGER,
    quantity INTEGER,
    order_date TEXT
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS customers (
    id INTEGER PRIMARY KEY,
    name TEXT,
    city TEXT
)
""")

# Insert sample data
customers_data = [(1, "Nguyễn Văn A", "Hà Nội"), (2, "Trần Thị B", "TP.HCM")]
orders_data = [(1, 1, 1, 2, "2024-01-01"), (2, 2, 2, 5, "2024-01-02")]

cursor.executemany("INSERT INTO customers VALUES (?, ?, ?)", customers_data)
cursor.executemany("INSERT INTO orders VALUES (?, ?, ?, ?, ?)", orders_data)
conn.commit()

# JOIN query
query = """
SELECT 
    c.name,
    c.city,
    o.quantity,
    o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.id
"""
df_joined = pd.read_sql_query(query, conn)
print("JOIN Result:")
print(df_joined)

conn.close()
```

### Aggregation Queries

```python
conn = sqlite3.connect("sales.db")

# Aggregation
query = """
SELECT 
    c.city,
    COUNT(o.id) as order_count,
    SUM(o.quantity) as total_quantity
FROM orders o
JOIN customers c ON o.customer_id = c.id
GROUP BY c.city
ORDER BY order_count DESC
"""

df_agg = pd.read_sql_query(query, conn)
print("Aggregation Result:")
print(df_agg)

conn.close()
```

## 6. Case Study: Phân Tích Dữ Liệu Từ Database

```python
import sqlite3
import pandas as pd
from datetime import datetime, timedelta

# Tạo database và dữ liệu mẫu
conn = sqlite3.connect("sales_analysis.db")
cursor = conn.cursor()

# Tạo tables
cursor.execute("""
CREATE TABLE IF NOT EXISTS sales (
    id INTEGER PRIMARY KEY,
    date TEXT,
    product_id INTEGER,
    quantity INTEGER,
    revenue REAL,
    region TEXT
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY,
    name TEXT,
    category TEXT,
    price REAL
)
""")

# Insert sample data
products = [
    (1, "Laptop", "Electronics", 15000000),
    (2, "Mouse", "Accessories", 500000),
    (3, "Keyboard", "Accessories", 2000000)
]

sales_data = []
base_date = datetime(2024, 1, 1)
for i in range(100):
    date = (base_date + timedelta(days=i)).strftime("%Y-%m-%d")
    product_id = (i % 3) + 1
    quantity = (i % 10) + 1
    revenue = quantity * products[product_id - 1][3]
    region = ["North", "South", "Central"][i % 3]
    sales_data.append((i + 1, date, product_id, quantity, revenue, region))

cursor.executemany("INSERT INTO products VALUES (?, ?, ?, ?)", products)
cursor.executemany(
    "INSERT INTO sales (id, date, product_id, quantity, revenue, region) VALUES (?, ?, ?, ?, ?, ?)",
    sales_data
)
conn.commit()

# 1. Tổng hợp doanh số theo sản phẩm
query1 = """
SELECT 
    p.name,
    p.category,
    SUM(s.quantity) as total_quantity,
    SUM(s.revenue) as total_revenue,
    AVG(s.revenue) as avg_revenue
FROM sales s
JOIN products p ON s.product_id = p.id
GROUP BY p.name, p.category
ORDER BY total_revenue DESC
"""

df_product_summary = pd.read_sql_query(query1, conn)
print("Tổng hợp theo sản phẩm:")
print(df_product_summary)

# 2. Doanh số theo khu vực
query2 = """
SELECT 
    region,
    COUNT(*) as order_count,
    SUM(revenue) as total_revenue,
    AVG(revenue) as avg_revenue
FROM sales
GROUP BY region
ORDER BY total_revenue DESC
"""

df_region_summary = pd.read_sql_query(query2, conn)
print("\nTổng hợp theo khu vực:")
print(df_region_summary)

# 3. Xu hướng theo thời gian
query3 = """
SELECT 
    date,
    SUM(revenue) as daily_revenue,
    SUM(quantity) as daily_quantity
FROM sales
GROUP BY date
ORDER BY date
"""

df_trend = pd.read_sql_query(query3, conn)
df_trend["date"] = pd.to_datetime(df_trend["date"])
print("\nXu hướng theo thời gian:")
print(df_trend.head(10))

conn.close()
```

## 7. Tối Ưu Performance

### Indexing

```python
# Tạo index để tăng tốc queries
cursor.execute("CREATE INDEX IF NOT EXISTS idx_product_id ON sales(product_id)")
cursor.execute("CREATE INDEX IF NOT EXISTS idx_date ON sales(date)")
cursor.execute("CREATE INDEX IF NOT EXISTS idx_region ON sales(region)")
```

### Batch Processing

```python
# Xử lý dữ liệu theo batch thay vì load hết
def process_in_batches(query, engine, batch_size=1000):
    """Xử lý dữ liệu theo batch"""
    offset = 0
    while True:
        batch_query = f"{query} LIMIT {batch_size} OFFSET {offset}"
        df = pd.read_sql(batch_query, engine)
        
        if df.empty:
            break
        
        # Xử lý batch
        yield df
        
        offset += batch_size
```

## 8. Best Practices

### 1. Connection Management

```python
# Sử dụng context manager
from contextlib import contextmanager

@contextmanager
def get_db_connection(db_path):
    conn = sqlite3.connect(db_path)
    try:
        yield conn
    finally:
        conn.close()

# Sử dụng
with get_db_connection("sales.db") as conn:
    df = pd.read_sql("SELECT * FROM products", conn)
```

### 2. Parameterized Queries

```python
# Tránh SQL injection
product_name = "Laptop"
query = "SELECT * FROM products WHERE name = ?"
df = pd.read_sql_query(query, conn, params=(product_name,))
```

### 3. Error Handling

```python
try:
    df = pd.read_sql("SELECT * FROM products", engine)
except Exception as e:
    print(f"Error reading from database: {e}")
    df = pd.DataFrame()  # Return empty DataFrame
```

## Bài Tập Thực Hành

### Bài 1: Tạo và Quản Lý Database
```python
# Tạo database với:
# - Table customers
# - Table orders
# - Table products
# Insert sample data
# Thực hiện JOIN queries
```

### Bài 2: Phân Tích Dữ Liệu
```python
# Từ database, tính:
# - Tổng doanh số theo sản phẩm
# - Doanh số theo khu vực
# - Top 10 khách hàng
# - Xu hướng theo thời gian
```

### Bài 3: Export và Báo Cáo
```python
# Tạo báo cáo từ database:
# - Tổng hợp dữ liệu
# - Export sang CSV/Excel
# - Tạo visualizations
```

## Tổng Kết

✅ Đã học:
- Kết nối với SQLite, PostgreSQL, MySQL
- Đọc và ghi dữ liệu với Pandas
- SQLAlchemy
- Complex queries (JOIN, aggregation)
- Tối ưu performance

**Tiếp theo**: [03. Automation & Scheduling](03-automation.md)

