# 03. Plotly

## Mục Tiêu
- Tạo interactive charts với Plotly
- Tạo dashboards tương tác
- Export charts sang HTML
- Sử dụng Plotly Express

## Plotly Là Gì?

Plotly tạo interactive visualizations:
- **Tương tác**: Zoom, pan, hover tooltips
- **Đẹp**: Charts chuyên nghiệp
- **Export**: HTML, PNG, PDF
- **Dashboards**: Tích hợp với Dash

## Cài Đặt

```bash
pip install plotly
```

## 1. Plotly Express (Đơn Giản)

### Line Plot

```python
import plotly.express as px
import pandas as pd
import numpy as np

# Dữ liệu
dates = pd.date_range("2024-01-01", periods=12, freq="M")
sales_data = pd.DataFrame({
    "Date": dates,
    "Laptop": [30 + i*2 for i in range(12)],
    "Mouse": [150 + i*5 for i in range(12)],
    "Keyboard": [80 + i*3 for i in range(12)]
})

# Line plot
fig = px.line(sales_data, x="Date", y=["Laptop", "Mouse", "Keyboard"],
              title="Doanh Số Theo Sản Phẩm",
              labels={"value": "Số Lượng Bán", "Date": "Tháng"})
fig.show()
```

### Bar Chart

```python
# Dữ liệu
products = pd.DataFrame({
    "Product": ["Laptop", "Mouse", "Keyboard", "Monitor", "Speaker"],
    "Sales": [450000000, 75000000, 160000000, 250000000, 120000000],
    "Category": ["Electronics", "Accessories", "Accessories", 
                 "Electronics", "Audio"]
})

# Bar chart
fig = px.bar(products, x="Product", y="Sales", color="Category",
             title="Doanh Số Theo Sản Phẩm",
             labels={"Sales": "Doanh Số (VND)", "Product": "Sản Phẩm"})
fig.show()
```

### Scatter Plot

```python
# Dữ liệu
sales_scatter = pd.DataFrame({
    "Price": [15000000, 500000, 2000000, 5000000, 3000000, 8000000],
    "Sales": [30, 150, 80, 50, 40, 25],
    "Category": ["Electronics", "Accessories", "Accessories",
                 "Electronics", "Audio", "Electronics"],
    "Revenue": [450000000, 75000000, 160000000, 250000000, 120000000, 200000000]
})

# Scatter plot
fig = px.scatter(sales_scatter, x="Price", y="Sales", 
                 size="Revenue", color="Category",
                 hover_data=["Revenue"],
                 title="Mối Quan Hệ Giữa Giá và Số Lượng Bán",
                 labels={"Price": "Giá (VND)", "Sales": "Số Lượng Bán"})
fig.show()
```

## 2. Plotly Graph Objects (Nâng Cao)

### Line Plot với Nhiều Traces

```python
import plotly.graph_objects as go

# Tạo figure
fig = go.Figure()

# Thêm traces
fig.add_trace(go.Scatter(
    x=dates,
    y=sales_data["Laptop"],
    mode='lines+markers',
    name='Laptop',
    line=dict(color='#2E86AB', width=2)
))

fig.add_trace(go.Scatter(
    x=dates,
    y=sales_data["Mouse"],
    mode='lines+markers',
    name='Mouse',
    line=dict(color='#A23B72', width=2)
))

# Layout
fig.update_layout(
    title="Doanh Số Theo Sản Phẩm",
    xaxis_title="Tháng",
    yaxis_title="Số Lượng Bán",
    hovermode='x unified',
    template="plotly_white"
)

fig.show()
```

### Bar Chart với Error Bars

```python
fig = go.Figure()

fig.add_trace(go.Bar(
    x=products["Product"],
    y=products["Sales"],
    error_y=dict(type='data', array=[10000000, 5000000, 8000000, 5000000, 6000000]),
    marker_color='#2E86AB',
    text=products["Sales"],
    texttemplate='%{text:,}',
    textposition='outside'
))

fig.update_layout(
    title="Doanh Số Theo Sản Phẩm",
    xaxis_title="Sản Phẩm",
    yaxis_title="Doanh Số (VND)",
    template="plotly_white"
)

fig.show()
```

## 3. Subplots

```python
from plotly.subplots import make_subplots

# Tạo subplots
fig = make_subplots(
    rows=2, cols=2,
    subplot_titles=("Doanh Số Theo Tháng", "Doanh Số Theo Sản Phẩm",
                    "Giá vs Số Lượng", "Phân Bố Doanh Số"),
    specs=[[{"secondary_y": False}, {"secondary_y": False}],
           [{"secondary_y": False}, {"type": "histogram"}]]
)

# Subplot 1: Line
fig.add_trace(
    go.Scatter(x=dates, y=sales_data["Laptop"], name="Laptop"),
    row=1, col=1
)

# Subplot 2: Bar
fig.add_trace(
    go.Bar(x=products["Product"], y=products["Sales"], name="Sales"),
    row=1, col=2
)

# Subplot 3: Scatter
fig.add_trace(
    go.Scatter(x=sales_scatter["Price"], y=sales_scatter["Sales"],
               mode='markers', name="Price vs Sales"),
    row=2, col=1
)

# Subplot 4: Histogram
fig.add_trace(
    go.Histogram(x=products["Sales"], name="Distribution"),
    row=2, col=2
)

fig.update_layout(height=800, title_text="Dashboard Doanh Số")
fig.show()
```

## 4. Heatmap

```python
# Correlation matrix
sales_corr = pd.DataFrame({
    "Laptop": np.random.randn(100),
    "Mouse": np.random.randn(100),
    "Keyboard": np.random.randn(100),
    "Monitor": np.random.randn(100)
})

correlation = sales_corr.corr()

fig = go.Figure(data=go.Heatmap(
    z=correlation.values,
    x=correlation.columns,
    y=correlation.columns,
    colorscale='RdBu',
    zmid=0,
    text=correlation.values,
    texttemplate='%{text:.2f}',
    textfont={"size": 10}
))

fig.update_layout(
    title="Correlation Matrix",
    width=600,
    height=600
)

fig.show()
```

## 5. 3D Scatter Plot

```python
# 3D scatter
fig = go.Figure(data=go.Scatter3d(
    x=sales_scatter["Price"],
    y=sales_scatter["Sales"],
    z=sales_scatter["Revenue"],
    mode='markers',
    marker=dict(
        size=10,
        color=sales_scatter["Revenue"],
        colorscale='Viridis',
        showscale=True
    ),
    text=sales_scatter["Category"],
    hovertemplate='<b>%{text}</b><br>' +
                  'Price: %{x:,.0f}<br>' +
                  'Sales: %{y}<br>' +
                  'Revenue: %{z:,.0f}<extra></extra>'
))

fig.update_layout(
    title="3D Scatter: Price, Sales, Revenue",
    scene=dict(
        xaxis_title="Price",
        yaxis_title="Sales",
        zaxis_title="Revenue"
    )
)

fig.show()
```

## 6. Export và Lưu

```python
# Export HTML
fig.write_html("sales_chart.html")

# Export PNG (cần kaleido)
# pip install kaleido
fig.write_image("sales_chart.png", width=1200, height=600)

# Export PDF
fig.write_image("sales_chart.pdf")
```

## 7. Tùy Chỉnh Layout

```python
fig = px.bar(products, x="Product", y="Sales", color="Category")

fig.update_layout(
    title={
        'text': "Doanh Số Theo Sản Phẩm",
        'x': 0.5,
        'xanchor': 'center',
        'font': {'size': 20}
    },
    xaxis_title="Sản Phẩm",
    yaxis_title="Doanh Số (VND)",
    font=dict(size=12),
    template="plotly_white",
    hovermode='x unified',
    showlegend=True,
    legend=dict(
        orientation="h",
        yanchor="bottom",
        y=1.02,
        xanchor="right",
        x=1
    )
)

# Format y-axis
fig.update_yaxes(tickformat=",.0f")

fig.show()
```

## 8. Annotations và Shapes

```python
fig = px.line(sales_data, x="Date", y="Laptop")

# Thêm annotation
fig.add_annotation(
    x="2024-06-30",
    y=sales_data["Laptop"].iloc[5],
    text="Peak Sales",
    showarrow=True,
    arrowhead=2,
    arrowcolor="red"
)

# Thêm shape (highlight area)
fig.add_shape(
    type="rect",
    x0="2024-03-01",
    x1="2024-05-31",
    y0=0,
    y1=100,
    fillcolor="yellow",
    opacity=0.2,
    line_width=0
)

fig.show()
```

## Bài Tập Thực Hành

### Bài 1: Interactive Dashboard
```python
# Tạo dashboard với 4 charts:
# 1. Line plot: Doanh số theo thời gian (có zoom)
# 2. Bar chart: Top sản phẩm (có hover)
# 3. Scatter plot: Giá vs Sales (có size theo Revenue)
# 4. Heatmap: Correlation matrix
```

### Bài 2: 3D Visualization
```python
# Tạo 3D scatter plot với:
# - X: Price
# - Y: Sales
# - Z: Revenue
# - Color: Category
# - Size: Profit
```

### Bài 3: Export và Share
```python
# Tạo báo cáo interactive:
# - Nhiều charts
# - Export HTML
# - Có annotations và highlights
# - Responsive layout
```

## Tổng Kết

✅ Đã học:
- Plotly Express (đơn giản)
- Plotly Graph Objects (nâng cao)
- Subplots
- Heatmaps
- 3D visualizations
- Export và tùy chỉnh

**Tiếp theo**: [Phần 4: Thống Kê & Phân Tích](04-statistics-analysis/01-descriptive-statistics.md)

