![StockX](https://www.bleepstatic.com/content/hl-images/2019/08/01/stockx-header-2.jpg)

# StockX Price Premium Preditive Analysis

> This project aims to investigate the features behind resale premiums on [StockX](https://stockx.com/sneakers) and their prediction power by conduct feature engineering and utilize external popularity index on different brands

[![License](http://img.shields.io/:license-mit-blue.svg?style=flat-square)](http://badges.mit-license.org)

- **ASSUMPTION**: Hot sneakers presents little seaonalities, and in this project we will not discuss time series analysis.

- **DATA**:

  ​	[StockX Data Challenge 2019](https://s3.amazonaws.com/stockx-sneaker-analysis/wp-content/uploads/2019/02/StockX-Data-Contest-2019-3.xlsx);

     

- **MODEL**:

|  Tree-based   | Linear             |
| :-----------: | ------------------ |
| Random Forest | Lasso              |
|    XGboost    | SVM(Linear Kernel) |

- **RESULT:** We have utilized our strategy and identified undervalued shoes through March 2020, and these shoes have increased their price within 80% of our prediction by the end of March 2020.

  *Out-of-Sample Results:*

| Sneaker Name                                            | Image                                                        | Retail | Most Recent Sale | Current Premium | Predicted Premium |
| ------------------------------------------------------- | ------------------------------------------------------------ | ------ | ---------------- | --------------- | ----------------- |
| **Air Jordan** 1 Retro High Travis Scott(**Tan/Brown**) | <img src="https://stockx-360.imgix.net/Air-Jordan-1-Retro-High-Travis-Scott/Images/Air-Jordan-1-Retro-High-Travis-Scott/Lv2/img01.jpg?auto=format,compress&w=559&q=90&dpr=2&updated_at=1550180948" width="200"/> | $175   | $797 - $1486     | 497.4%          | **802.0%**        |
| **Blazer** Mid 77 Vintage Slam Jam(**White**)           | <img src="https://stockx-360.imgix.net/Nike-Blazer-Mid-77-Vintage-Slam-Jam/Images/Nike-Blazer-Mid-77-Vintage-Slam-Jam/Lv2/img01.jpg?auto=format,compress&w=559&q=90&dpr=2&updated_at=1554253378" width="200"/> | $100   | $470 - $486      | 381.0%          | **455.5%**        |
| **Yeezy** Boost 350 V2 Tail Light(**Grey, Orange**)     | <img src="https://stockx-360.imgix.net/adidas-Yeezy-Boost-350-V2-Tail-Light/Images/adidas-Yeezy-Boost-350-V2-Tail-Light/Lv2/img36.jpg?auto=format,compress&w=559&q=90&dpr=2&updated_at=1584757351" width="200"/> | $220   | $289 - $336      | 31.8%           | **82.0%**         |

 																										(*The price data below is based on March 2020)

## Table of Contents

* [Motivation](#motivation)
* [Data Source](#data_source)
* [Exploratory Data Analysis](#exploratory_data_analysis)
  * [Anomaly Dectection](#anomaly_detection)
  * [Time Feature](#time_feature)
  * [Color Feature](#color_feature)
  * [Region Feature](#region_feature)
* [Usage](#usage)
* [Roadmap](#roadmap)
* [Contributing](#contributing)
* [License](#license)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

## Motivation

<img src="https://stockx-360.imgix.net/Nike-Air-Yeezy-2-Red-October/Images/Nike-Air-Yeezy-2-Red-October/Lv2/img19.jpg?auto=format,compress&w=559&q=90&dpr=2&updated_at=1538080256" width = 300/>

The above is the famous Red Nike Yeezy. Its retail price is $250, and the latest resale price is $6,200, marked up by nearly 2400%. The high resale premium of this pair is not a single event. The once niche market of sneaker resale has grown to become a $2 billion market, and it is projected to reach $6 billion by 2025. Within the sneaker resale market, StockX is one of the largest platforms. The website operates like a stock exchange, where users can place a bidding or asking prices, and a deal is made whenever there’s a match. What is so valuable for us is that the platform offers transparent and actionable data. Using such data, we want to build a predictive model to identify undervalued sneakers, which resellers can invest in now and sell at higher price later.

## Data Source

We utilized public data offered through the [StockX Data Contest](https://stockx.com/news/the-2019-data-contest/), which consists of 99,956 transactions from 2017 to 2019. The dataset consists of two brands -- Yeezy and Nike Off-White, and over 50 different styles. 

To expand upon the features, we also manually obtained the colorway and number of sales from the StockX website. We then converted style and color into dummy variables, and sneaker size into frequency encoding as common shoe sizes are sold at higher premium. For modeling purposes, our target variable is price premium, which equals price markup over retail price, and our input variables are days since release, style, colorway, size, and number of sales.
Initial Data Preprosessing:

![data_source](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/data_source.png)

## Exploratory Data Analysis

From 

```html
<img src="image1.png" width="425"/> <img src="image2.png" width="425"/> 
```
![alt-text-1](data:image/png;base64,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%0A "title-1") ![alt-text-2](image2.png "title-2")
































