```python
%pip install nbformat
%pip install --upgrade plotly
```


```python
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```


```python
import plotly.io as pio
pio.renderers.default = "iframe"
```


```python
def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
    from IPython.display import display, HTML
    fig_html = fig.to_html()
    display(HTML(fig_html))
```

In this section, we define the function make_graph. You don't have to know how the function works, you should only care about the inputs. It takes a dataframe with stock data (dataframe must contain Date and Close columns), a dataframe with revenue data (dataframe must contain Date and Revenue columns), and the name of the stock.



```python
TESLA=yf.Ticker("TSLA")

## to display tesla's informations
TESLA.info
```


```python
## to show the stock history of tesla

tesla_data=TESLA.history(period="max")

tesla_data
```


```python
tesla_data.reset_index(inplace=True)
```


```python
tesla_data.head()
```

Question 2




```python
url="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
response=requests.get(url)

html_data=response.text

soup=BeautifulSoup(html_data,"html.parser")
```


```python
df=[]
for row in soup.find_all("tbody")[1].find_all("tr"):
    cells=row.find_all("td")
    
    if len(cells)==2:
     df.append({"Date":cells[0].text,
               "Revenue":cells[1].text})

tesla_revenue=pd.DataFrame(df) 
tesla_revenue        
     
     
     
```


```python
## this is gibven line by ibm to remove the comma and dollar sign from the Revenue column.  
tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(',|\$',"",regex=True)
```


```python
## given code to remove null values
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
```


```python
tesla_revenue
```


```python
tesla_revenue.tail()
```


```python
##Question 3
Gamestop=yf.Ticker("GME")
```


```python
## to get the stock history
gme_data=Gamestop.history(period="max")

```


```python
gme_data.reset_index(inplace=True)
```


```python
gme_data.head()
```


```python
##Question 4 
## extractiong gamestop revenue
url="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
res=requests.get(url)
html_data_2=res.text
soup=BeautifulSoup(html_data_2,"html.parser")

## using read html
df1=pd.DataFrame(columns=["Date","Revenue"])
gme_revenue=pd.read_html(url)
gme_revenue=gme_revenue[1]
gme_revenue.rename(columns={"GameStop Quarterly Revenue (Millions of US $)":"Date","GameStop Quarterly Revenue (Millions of US $).1":"Revenue"},inplace=True)

```


```python
## this is gibven line by ibm to remove the comma and dollar sign from the Revenue column.  
gme_revenue["Revenue"] = gme_revenue['Revenue'].str.replace(',|\$',"",regex=True)


```


```python
## given code to remove null values

gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
```


```python
gme_revenue.tail()
```


```python
## Question 5: Plot Tesla Stock Graph

make_graph(tesla_data,tesla_revenue,"Tesla")
```


```python
### Question 6: Plot GameStop Stock Graph

make_graph(gme_data,gme_revenue,"Game Stop")
```
