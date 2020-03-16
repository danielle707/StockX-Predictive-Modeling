![StockX](https://www.bleepstatic.com/content/hl-images/2019/08/01/stockx-header-2.jpg)

# StockX Price Premium Preditive Analysis

> This project aims to investigate the features behind resale premiums on [StockX](https://stockx.com/sneakers) and their prediction power by conduct feature engineering and utilize external popularity index on different brands



[![License](http://img.shields.io/:license-mit-blue.svg?style=flat-square)](http://badges.mit-license.org)

> RESULT:

> ASSUMPTION:

> DATA:

<<<<<<< HEAD
<br> [StockX Data Challenge 2019](https://s3.amazonaws.com/stockx-sneaker-analysis/wp-content/uploads/2019/02/StockX-Data-Contest-2019-3.xlsx)
<br> [Google Trend]()
=======
[StockX Data Challenge 2019](https://s3.amazonaws.com/stockx-sneaker-analysis/wp-content/uploads/2019/02/StockX-Data-Contest-2019-3.xlsx);\n
[Google Trend]()
>>>>>>> 0df9ee7650ff632cd7cade710cfebf74370fbbcd


> MODEL:

GBRT;
2-layer NN

---
## Table of Contents

- [Abstract](#abstract)
- [Data Source](#dataSource)
- [Exploratory Data Analysis](#exploratoryDataAnalysis)
- [Benchmark](#benchmark)
- [Sentiment Analysis](#sentimentanalysis)
- [Trading Strategy](#tradingstrategy)
- [Conclusion](#conclusion)
- [Team](#team)

---

## Data Source

Stockx Prices from Sep 1st 2017 to Feb 13th 2019:

Data cleaning: turn categorical variables into dummies, including brands, color and region
```python
stockx_data["yeezy"] = stockx_data['Sneaker Name'].apply(lambda x : 1 if 'Yeezy' in x.split("-") else 0) ## etc
```


Data Merging: match a sale with certain shoe size and date with corresponding google trend index

```python
#--- Get Google trend index---
#Step 1: get week number of google index on every sneaker style 
trend['weeknum'] = trend['week'].apply(lambda x : x.strftime("%V"))
#Step 2: get week number of stockx_data on every sale
stockx_data['weeknum'] = stockx_data["Order Date"].apply(lambda x : x.strftime("%V"))
#Step 3: Merge two tables by weeknum
stockx_data = stockx_data.merge(trend, left_on = 'weeknum', right_on = 'weeknum', how = 'left')
#Step 4: Multiply style binary variables to get corresponding google trend index
stockx_data['trend'] = (stockx_data['yeezy'].mul(stockx_data['Adidas Yeezy']) + stockx_data['airjordan'].mul(stockx_data['Air Jordan'])
                        + stockx_data['airforce'].mul(stockx_data['Nike Air Force'])+
                        stockx_data['airmax90'].mul(stockx_data['Nike Air Max']) +
                        stockx_data['airmax97'].mul(stockx_data['Nike Air Max']) +
                        stockx_data['presto'].mul(stockx_data['Nike Presto']) +
                        stockx_data['vapormax'].mul(stockx_data['Nike Air VaporMax']) +
                        stockx_data['blazer'].mul(stockx_data['Nike Blazers']) + 
                        stockx_data['zoom'].mul(stockx_data['Nike Zoom']) + 
                        stockx_data['react'].mul(stockx_data['Nike React']))
```
---
Dataset after processing:
[data prep with trend]
(https://github.com/danielle707/stockx_machine_learning_analysis/blob/master/data/media/data_prep.jpg)




## Exploratory Data Analysis



## License

[![License](http://img.shields.io/:license-mit-blue.svg?style=flat-square)](http://badges.mit-license.org)
