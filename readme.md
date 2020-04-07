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
* [Data Source](#data-source)
* [Exploratory Data Analysis](#exploratory-data-analysis)
  * [Anomaly Dectection](#anomaly-detection)
  * [Time Feature](#time-feature)
  * [Color Feature](#color-feature)
  * [Region Feature](#region-feature)
* [Modeling](#modeling)
  - [Feature Engineering](#feature-engineering)
  - [Model Selection](model-selection)
  - [Result](result)
* [Prediction Result](prediction_result)
* [Limitation](#limitation)
* [Improvement](improvement)
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

Exploring the price premium in our dataset, we found the it's heavily skew to the right, and it condensed in the range(0,3), indicating most sales achieve 4 times transaction price over its original retail one. Notably, there are huge amounts of transactions happen in range beyond 5, heading over 20 times premium. Though some of them are within the reasonable curve, and we would first conduct anomaly detection to discover those anomaly points and found out common features among those outliers.

![targetvalue](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/y_plot.png "Histogram Plot on Y") ![voilinplot](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/volinplot.png "Violin Plot on Y")

### Anomaly Detection

Since target value is heavily right skewed and positive, taking logrithms would have more robust result in outlier detection.

- **Step 1** Train an isolation forest on target value, using decision rules to find out those outliers.

~~~python
```python
model=IsolationForest(n_estimators=100, max_samples='auto', contamination= 0.05 ,max_features=1.0)
model.fit(y[['Pct_change']])
y['scores']=model.decision_function(y[['Pct_change']])
y['anomaly']=model.predict(y[['Pct_change']])

#IsolationForest(behaviour='deprecated', bootstrap=False, contamination=0.05,
#                max_features=1.0, max_samples='auto', n_estimators=100,
#                n_jobs=None, random_state=None, verbose=0, warm_start=False)
```
~~~

- **Step 2** Create anomaly lists and compare it to non-anomaly points

| Metric | whole | normal | Anamoly |
| :----: | :---: | :----: | :-----: |
|  Mean  | 1.25  |  1.03  |  5.40   |
| Median | 0.70  |  0.68  |  5.00   |

![anomalycomp](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/anomalycomp.png)

Most Anomaly points lie on the right tail of distribution, and their cut-off(using median statistics) is approximately exp(5), this is a cruicial indicator that if our prediction is beyond 100 times premium, there is large probability the point is an outlier and some statistically important features are underneath the pair of shoe. 

- **Step 3** Explore anomaly points

  Group anomaly points on their three features: brand, color and region, we could peek into what features are heavily weighted in our dataset.

  **Brand** Air Jordan contributes highest number of price premium among other brands, the anomaly amount has been more than twice larger than the second highest. The top 3 brands, i.e. Air Jordan, Presto, Blazer are all Nike and following these three are the Yeezy brand.

  **Color** White color is the dominating color feature. There are two hypothesis on why the number is high: 

  1. Most sneakers are white; 2. White is indeed a significant feature. To test our hypothesis, we will further find out which specific sneakers contribute. 

  **Region** Other than unnamed states, California and New York has highest state price premium. However, this doesn't mean these two states are have per capita premium. Stay around for further analysis in per capital level.

![anomoly_brand](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/ano_brand.png "Anomaly Points-Brand") ![anomaly_color](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/ano_color.png "Anomaly Points-Color") ![anomaly_region](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/ano_region.png "Anomaly Points-Region")

### Time Feature

![alltime](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/alltime.png)



![allregion](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/allregion.png) ![allstyle](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/allstyle.png) 

- Time effect on Nike

![allnike](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/allnike.png)

Top 3 nike brands

<img src="https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timeaj.png" width="250"/> <img src="https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timepresto.png" width="250"/> <img src="https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timezoom.png" width="250"/> 

![timeaj](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timeaj.png) ![timepresto](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timepresto.png) ![timepresto](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timezoom.png) 

- Time effect on Yeezy

![timeyeezy](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/timeyeezy.png)

Statistically, the best time to resell is 3 to 5 weeks before the release date 0. The worst time to resell is the first 9 weeks after release, when the market is saturated. After that, as the availability in market declines, buyers are willing to pay higher premiums. 

### Color Feature

Our dataset consists of two major brands – yeezy and nike off-white. In terms of yeezy, we can see that basic colors including black, white, and grey have constant growth. Bolder colors like orange would start high but decline as time passes. In terms of off-shite, red is the most popular color.

### Region Feature

Looking at the number of sales, California ranks the first and Oregon ranks the third. However, when we look at the percentage of population that purchases from StockX, Oregon comes to the top with nearly 2 transactions per 1000 people. This means that StockX might want to make more promotions in Oregon, but keep in mind that as a reseller, we cannot control the sales region.

![edaregion](https://github.com/danielle707/StockX-Predictive-Modeling/blob/master/data/media/edaregion.png)

## Modeling 

### Feature Engineering

To recapture the features in our datasets, the target variable is the price premium, calculated as the percentage change of sale price over retail price, and input features are days since release, brand, region, colorway and number of sales, adding up to 31 variables.

### Model Selection

In our project, we’ve tried 2 types of machine learning models, linear and tree-based regression ones. Due to different implementation and library on these algorithms, we've consolidate our model 

1. the same data train-test split and 5-fold validation fold (same random seed)
2. the same implemtation package GridSearchCV
3. using r2 as our evaluation metric

- **Linear Model - Lasso**

  We see from the Exploratory Data Analysis step, some brands or some colors (e.x. white) have over emphasizing power. In consideration of outliers and overfitting issues, we utilize Lasso model  to ensure regularization.

  ```python
  lasso = Lasso()
  parameters = {'alpha': [1e-5,1e-4,1e-3,1e-2,1e-1,1e0,1e1,1e2,1e3,1e4,1e5]}
  r2 = make_scorer(r2_score, greater_is_better=True)
  clf = GridSearchCV(lasso, 
                     parameters, 
                     cv=5,
                     scoring=r2)
  ```

- **Linear Model - SVM **

  ```python
  x.train = preprocessing.scale(x_train)
  linearsvr = LinearSVR(tol=0.01)
  parameters = {'C': [0.01, 0.1, 1, 10, 100]} 
  r2 = make_scorer(r2_score, greater_is_better=True)
  clf = GridSearchCV(linearsvr, 
                     parameters, 
                     cv=5, 
                     scoring=r2)
  ```



- **Tree-based - XGBoost**

  ```python
  params = {'colsample_bytree': [i/10. for i in range(8,11)],
            'subsample': [i/10. for i in range(8,11)],
            'eta': [.3, .4, .5],
            'max_depth': list(range(3,6)),
            'min_child_weight': list(range(4,7)),
            'eval_metric': ['rmse'],
            'objective': ['reg:squarederror']}
  xg_reg = xgb.XGBRegressor()
  r2 = make_scorer(r2_score, greater_is_better=True)
  clf = GridSearchCV(xg_reg, 
                     params, 
                     cv=5, 
                     scoring=r2)
  ```

  



## Prediction Result



## Limitation

**1. Brand Generalisation** The data we utilized only cover sales from 2017 to 2019. Due to the changing tendency of the shoe market, this model’s accuracy and predictability for future price premium might be less stable.(added) 

**2. Timing Data Limitation** More other brands’ data and recent sales data could improve the generalization and future predictability of this model. Furthermore, More features data, such as the material or color purity of shoes, could be used to improve our model.

## Improvement

1. Time Series consideration, 
2. DNN Model

