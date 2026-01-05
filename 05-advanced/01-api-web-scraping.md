# 01. API & Web Scraping

## Mục Tiêu
- Làm việc với APIs (REST APIs)
- Thu thập dữ liệu từ web (web scraping)
- Xử lý JSON data
- Lưu dữ liệu vào file

## Tại Sao Cần API & Web Scraping?

Data Analyst cần dữ liệu từ nhiều nguồn:
- **APIs**: Dữ liệu real-time từ services
- **Web scraping**: Thu thập dữ liệu từ websites
- **Tự động hóa**: Thu thập dữ liệu định kỳ

## 1. Working with APIs

### Requests Library

```python
import requests
import pandas as pd
import json

# GET request đơn giản
response = requests.get("https://api.github.com/users/octocat")
print(f"Status code: {response.status_code}")

if response.status_code == 200:
    data = response.json()
    print(json.dumps(data, indent=2))
else:
    print(f"Error: {response.status_code}")
```

### API với Parameters

```python
# API với query parameters
url = "https://jsonplaceholder.typicode.com/posts"
params = {
    "userId": 1,
    "_limit": 5
}

response = requests.get(url, params=params)
posts = response.json()

# Chuyển sang DataFrame
df = pd.DataFrame(posts)
print(df.head())
```

**Ví dụ thực tế**: Lấy dữ liệu từ API công khai
```python
# Ví dụ: Lấy dữ liệu thời tiết (mock API)
# Trong thực tế, bạn cần API key từ weather service

# Mock data structure
weather_data = {
    "location": "Hà Nội",
    "temperature": 25,
    "humidity": 70,
    "wind_speed": 15
}

# Giả sử đây là response từ API
response_data = weather_data

# Chuyển sang DataFrame
weather_df = pd.DataFrame([response_data])
print("Dữ liệu thời tiết:")
print(weather_df)
```

### API với Authentication

```python
# API với API key
api_key = "your_api_key_here"
headers = {
    "Authorization": f"Bearer {api_key}",
    "Content-Type": "application/json"
}

# Hoặc trong URL
url = f"https://api.example.com/data?api_key={api_key}"

# POST request
data = {
    "name": "Product A",
    "price": 1000000,
    "quantity": 50
}

response = requests.post(url, json=data, headers=headers)
```

## 2. Web Scraping với BeautifulSoup

### Cài Đặt

```bash
pip install beautifulsoup4 requests lxml
```

### Scraping Cơ Bản

```python
from bs4 import BeautifulSoup
import requests

# Lấy HTML
url = "https://example.com"
response = requests.get(url)

if response.status_code == 200:
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Lấy title
    title = soup.find('title')
    print(f"Title: {title.text}")
    
    # Lấy tất cả links
    links = soup.find_all('a')
    for link in links[:5]:  # 5 links đầu
        print(f"Link: {link.get('href')}")
```

### Scraping với Selectors

```python
# Tìm elements bằng CSS selectors
# Giả sử có HTML structure:
# <div class="product">
#   <h2 class="name">Product Name</h2>
#   <span class="price">1000000</span>
# </div>

html_content = """
<div class="product">
    <h2 class="name">Laptop</h2>
    <span class="price">15000000</span>
</div>
<div class="product">
    <h2 class="name">Mouse</h2>
    <span class="price">500000</span>
</div>
"""

soup = BeautifulSoup(html_content, 'html.parser')

# Tìm tất cả products
products = soup.find_all('div', class_='product')

products_data = []
for product in products:
    name = product.find('h2', class_='name').text
    price = product.find('span', class_='price').text
    products_data.append({
        "Name": name,
        "Price": int(price)
    })

# Chuyển sang DataFrame
df = pd.DataFrame(products_data)
print(df)
```

**Ví dụ thực tế**: Scraping dữ liệu sản phẩm
```python
# Mock HTML structure
html = """
<table class="products">
    <tr>
        <td>Laptop</td>
        <td>15000000</td>
        <td>50</td>
    </tr>
    <tr>
        <td>Mouse</td>
        <td>500000</td>
        <td>200</td>
    </tr>
    <tr>
        <td>Keyboard</td>
        <td>2000000</td>
        <td>100</td>
    </tr>
</table>
"""

soup = BeautifulSoup(html, 'html.parser')
table = soup.find('table', class_='products')
rows = table.find_all('tr')[1:]  # Bỏ header

products = []
for row in rows:
    cols = row.find_all('td')
    products.append({
        "Product": cols[0].text,
        "Price": int(cols[1].text),
        "Stock": int(cols[2].text)
    })

df = pd.DataFrame(products)
print("Dữ liệu sản phẩm:")
print(df)
```

## 3. Xử Lý JSON Data

### Đọc JSON

```python
import json

# JSON string
json_string = '''
{
    "products": [
        {"name": "Laptop", "price": 15000000, "stock": 50},
        {"name": "Mouse", "price": 500000, "stock": 200},
        {"name": "Keyboard", "price": 2000000, "stock": 100}
    ]
}
'''

# Parse JSON
data = json.loads(json_string)

# Chuyển sang DataFrame
df = pd.DataFrame(data["products"])
print(df)
```

### Đọc JSON từ File

```python
# Đọc từ file
with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)

# Hoặc với pandas
df = pd.read_json("data.json")
```

### Ghi JSON

```python
# Ghi ra file
data = {
    "products": [
        {"name": "Laptop", "price": 15000000},
        {"name": "Mouse", "price": 500000}
    ]
}

with open("output.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=2, ensure_ascii=False)

# Hoặc từ DataFrame
df.to_json("output.json", orient="records", indent=2, force_ascii=False)
```

## 4. Rate Limiting và Error Handling

### Rate Limiting

```python
import time

def fetch_with_delay(url, delay=1):
    """Fetch với delay để tránh rate limit"""
    response = requests.get(url)
    time.sleep(delay)  # Đợi 1 giây
    return response
```

### Error Handling

```python
def safe_request(url, max_retries=3):
    """Request với retry logic"""
    for attempt in range(max_retries):
        try:
            response = requests.get(url, timeout=10)
            response.raise_for_status()  # Raise error nếu status code không OK
            return response
        except requests.exceptions.RequestException as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            if attempt < max_retries - 1:
                time.sleep(2 ** attempt)  # Exponential backoff
            else:
                raise
    return None
```

## 5. Case Study: Thu Thập Dữ Liệu Từ Nhiều Nguồn

```python
# Giả sử cần thu thập dữ liệu từ:
# 1. API (dữ liệu real-time)
# 2. Web scraping (dữ liệu từ website)
# 3. File JSON (dữ liệu lịch sử)

# 1. Lấy dữ liệu từ API
def fetch_api_data():
    """Lấy dữ liệu từ API"""
    # Mock API response
    api_data = {
        "current_sales": 50000000,
        "products": [
            {"name": "Laptop", "sales": 30},
            {"name": "Mouse", "sales": 150}
        ]
    }
    return api_data

# 2. Scraping dữ liệu
def scrape_website_data():
    """Scraping dữ liệu từ website"""
    # Mock scraped data
    scraped_data = {
        "competitor_prices": [
            {"product": "Laptop", "price": 14500000},
            {"product": "Mouse", "price": 480000}
        ]
    }
    return scraped_data

# 3. Đọc dữ liệu từ file
def load_historical_data():
    """Đọc dữ liệu lịch sử"""
    historical = {
        "monthly_sales": [
            {"month": "Jan", "sales": 50000000},
            {"month": "Feb", "sales": 55000000},
            {"month": "Mar", "sales": 60000000}
        ]
    }
    return historical

# Tổng hợp dữ liệu
api_data = fetch_api_data()
scraped_data = scrape_website_data()
historical_data = load_historical_data()

# Chuyển sang DataFrames
api_df = pd.DataFrame(api_data["products"])
scraped_df = pd.DataFrame(scraped_data["competitor_prices"])
historical_df = pd.DataFrame(historical_data["monthly_sales"])

print("Dữ liệu từ API:")
print(api_df)
print("\nDữ liệu từ scraping:")
print(scraped_df)
print("\nDữ liệu lịch sử:")
print(historical_df)

# Lưu vào file
api_df.to_csv("api_data.csv", index=False)
scraped_df.to_csv("scraped_data.csv", index=False)
historical_df.to_csv("historical_data.csv", index=False)
```

## 6. Best Practices

### 1. Respect robots.txt

```python
# Kiểm tra robots.txt trước khi scrape
import urllib.robotparser

rp = urllib.robotparser.RobotFileParser()
rp.set_url("https://example.com/robots.txt")
rp.read()

if rp.can_fetch("*", "https://example.com/page"):
    # Có thể scrape
    pass
else:
    # Không được scrape
    print("Not allowed to scrape this page")
```

### 2. User-Agent Headers

```python
# Thêm User-Agent để tránh bị block
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
}

response = requests.get(url, headers=headers)
```

### 3. Session Management

```python
# Sử dụng session để tái sử dụng connection
session = requests.Session()
session.headers.update({"User-Agent": "MyApp/1.0"})

response1 = session.get("https://example.com/page1")
response2 = session.get("https://example.com/page2")
```

## Bài Tập Thực Hành

### Bài 1: API Integration
```python
# Tạo function để:
# 1. Lấy dữ liệu từ API công khai (ví dụ: JSONPlaceholder)
# 2. Xử lý và chuyển sang DataFrame
# 3. Lưu vào CSV
```

### Bài 2: Web Scraping
```python
# Scrape dữ liệu từ một trang web:
# 1. Lấy HTML
# 2. Parse với BeautifulSoup
# 3. Extract dữ liệu cần thiết
# 4. Lưu vào DataFrame và CSV
```

### Bài 3: Data Aggregation
```python
# Thu thập dữ liệu từ nhiều nguồn:
# - API
# - Web scraping
# - File JSON
# Merge và tổng hợp dữ liệu
```

## Tổng Kết

✅ Đã học:
- Làm việc với REST APIs
- Web scraping với BeautifulSoup
- Xử lý JSON data
- Error handling và rate limiting
- Best practices

**Tiếp theo**: [02. Working with Databases](02-databases.md)

