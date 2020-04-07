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

```html
<p float="left">
  <img src="data:image/png;base64,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%0A" width="200" />
  
  <img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAV0AAAEJCAYAAAA3l/RWAAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAALEgAACxIB0t1+/AAAADh0RVh0U29mdHdhcmUAbWF0cGxvdGxpYiB2ZXJzaW9uMy4xLjAsIGh0dHA6Ly9tYXRwbG90bGliLm9yZy+17YcXAAAe3UlEQVR4nO3de3RU5b038O+ePXsyuUySyY2LJBEJBBACVHnlJjm6arwkgBw5WFBZvryn2tcePcvX6qp6WlflrWWhvh6s7aLvUWtdulpbjBewtqAerRV8j9ISoEoCcg2EkPt1Lnv2ft4/YqaQC5Aw+3kyk+9nrazFTGbv/XM768vD73n23poQQoCIiKRwqS6AiGg0YegSEUnE0CUikoihS0QkEUOXiEgi92C/sG0bXV1dMAwDmqbJrImIKG4JIWCaJlJTU+Fy9R/XDhq6XV1dqKmpcbQ4IqJENWXKFPh8vn7vDxq6hmFEN/R4PDEtZt++fZgxY0ZM95lIeH7Ojefn/HiOzs3J8xMOh1FTUxPN0L4GDd3eloLH40FSUlLMC3Nin4mE5+fceH7Oj+fo3Jw+P4O1ZTmRRkQkEUOXiEgihi4RkUQMXSIiiRi6REQSMXSJiCRi6BIRScTQJSKSiKFLRCQRQ5eISCKGLhGRRAxdIiKJGLpERBIxdImIJGLoEhFJxNAlIpKIoUtEJBFDl4hIIqWhu2fPHrz99tsqSyAikkpp6G7fvh0vvvgiwuGwyjKIiKRRGrpdXV2wLAuHDh1SWQYRkTTKQxcA9u/fr7IMIiJplIZuR2cnAKC6ulplGURE0ige6XYDAL78kiNdIhodlIZuoLsb0HQ0NTWiqalJZSlERFIoC13bthEMBqCn5AIAampqVJVCRCSNstANBAIQQkBPyYOmuTiZRkSjgrLQ7V25oLmT4PL6GbpENCqoD12XB67kbBw4cBCRSERVOUREUigL3e7unpULmm5AT86BaYZx5MgRVeUQEUkxIka6enI2AK7XJaLEpz50dQOaOwW6kczQJaKEp7y9AJcBTdOgJWXhiy+/VFUOEZEUykK38+tLgDWX0VNIcjbqT51CR0eHqpKIiByndKSruXRoLh0Aon3dAwcOqCqJiMhxSnu6Lt0Tfa17swBwMo2IEpvS0NV0I/pa0w3o3gxeDkxECU1pe0FoxlnvaUlZ2L+/GkIIRVURETlLWeh2dHQCrrNDV0/ORmdnB+rr6xVVRUTkLLXthb6h+3Vfl5NpRJSo1C0Z69PTBQCXNxOaS+dkGhElLHW3duzuBlyes97TNBdcXj+qqzmZRkSJSUnoRiIRmGa430gXAFzeLHz11Ve84xgRJSQloRsKhQCgX08XAHRvNkwzjKNHj8oui4jIcUpCNxgMAsCAI93eK9O4XpeIEpHa0O3T0wUAzUiF7vZyBQMRJSSl7QUMMNLVNA1I8vOx7ESUkBSPdPuHLtDTYqg9UYva2lqZZREROW5Ehq7hL4LLZWDTpk28JJiIEora1Qt6/54uALjcXhg5M1BVVYU///nPMksjInKU0pEuXO5BP2P4i6An+/F//+M//v6UCSKiOKcsdF26B5o2+OE1zYWkMVegtaUFv/71ryVWR0TkHGXthYHW6PalJ+fAyLgMb7/9Ntra2iRURkTkLGUj3cEm0fpypxfAtm0cO3bM4aqIiJynLHT73sB8MK4kHwBw+RgRJQR1E2kX0F4AAM2dAs3lxokTJxyuiojIeUpCNzCE9oKmadA9aQxdIkoIaibSghc2kRZl+HD8ONsLRBT/pIeuEAKh0IWPdAHA5fGhoeE0TNN0sDIiIudJD91QKNRzae8gV6MNxJXkg23bOHXqlIOVERE5T3rodnV1ARj8vgsDcXnSAYB9XSKKe3ESuj3Lxhi6RBTvpIdu730UBrvZzUA03QPdSGboElHckx66nZ2dAIY20gUAGGm8QIKI4p6yke6FXhzRy2X4UFvLkS4RxTeFPd0Lby8APSsYOjraoyNlIqJ4pC50hzrS5QoGIkoAatoLmgZo+pC207iCgYgSgJKJtJ4bmGtD2s7lSQU0jaFLRHFNyUh3qK0FANA0HbrHx9AlorgmPXTb29uBIU6iRRlpOH78eGwLIiKSSHroNjU3A7p3WNu6PD7U1dXBtu0YV0VEJIf00G1pboHLnTysbV0eH0zTRENDQ4yrIiKSQ2roWpaFzs4OaO7hjXR1rx8AUFNTE8uyiIikkRq6ra2tEEJAG+5I1+uHSzewd+/eGFdGRCSH1NBtaWkBgGGPdDXNBVdyDvbsYegSUXySGrrNzc09Bx3mSBcAXCl5OHGiNhrgRETxJK5GugDgTskDAOzbty8mNRERyaRkpHsxocu+LhHFM+kjXd3thTbE+y6cSdNc0Lw5qKraE8PKiIjkkB66FzPK7eVOzcPJkyfY1yWiuCM1dJuamiH0pIvej86+LhHFKbk93ZbmYa/RPRP7ukQUr6SFrhACra2t0IZ534Uzsa9LRPFKWuh2dnbCikQuao3umdypY3Dy5InoiggionggLXRjsUb3THrqWADAe++9F5P9ERHJEL+h682EO+0SvP7663xYJRHFDWmhG4tLgPvy5M5Ad3c33nzzzZjtk4jISQpGurELXd3rh9uXjzfffAttbW0x2y8RkVOkhq7mcgMud0z368mdiVAohM2bN8d0v0RETpDaXtCN5CE/Bfh89KR0uDMuxdatW9HY2BjTfRMRxZrU0I3F1WgDScq5HJYNbNy4kc9PI6IRTWLotkDTY9fPPZPLkwZP3mzs3r0bb731liPHICKKhdg2WM+hpaUFmvcSx/ZvZE6C1XUKv/rVrzBz5kwUFRVd0Ha2beOTTz5BXV0dUlJSkJKSgoKCAkyaNCnmrRAiIimhGwqFEAh0w5MWmzW6A9E0Dd5xcxE48kds2PAk/v3fn0FKSso5t9mzZw+ef/55HD58uN/vCgoKUFZWhgULFiA7Oxsul/QHJxNRApISur3LxWK5Rncgmp4Ez9irUHfsQ9z3r/+K++69FyUlJWd9RgiBPXv2oLKyEn/5y1+ge1LhHT8Pbl8+hG0CtolIVz1ONBzG888/j+effx66riMrOweXFhZi0aKFmDdv3nkDnYhoIFJDN5ZrdAfjTh2D5IJ/QGP953j00UdRVlaGkpISBINBdHZ24oMPPsCxY8fgcnvhyZ0FT9bknqVsADSXDsALj8cHj78IVrAVVncDRKQbLcEuNFf9DZ999l9wGwbmz5uHZcuWobi42PH/JiJKHFJCNxaP6RkKd+oY6Jdej3DjPmzbvh3btm2L/k73ZsI77r/BnV74dcgOTvdmQvdmRl8LIWAHmmC2H8UnO/8fPv74Y0ybPh0rbrkFc+fOZQ+YiM4r4Ua6vTSXG0l5s2H4p0DYEWgu/euLMzzDDkdN06Cn5EBPyYHILYHZdgg1B2uwbt06TJpUhNtvvw1XXHEFw5eIBiUvdDUNmu6RcbizuAxneq+absCTVQzDPxmRtiM4cvxv+NGPfoTi4mKsXLkSV155JSffiKgfaaHbczVa4oWQprlgZF4Gd0YhzNbDOHj4S6xbtw4T8vNxyz/+IxYsWMBJNyKKkhK6DQ0NQAyeGDGSaZoOj78IRuZliLQfQ13DfmzcuBHPPfccZs6ciXnz5mHevHnIzs5WXSoRKeR46O7cuRO7d++GkTU6Zvk1zQUj41K40wthBRoQ6TiJvV8ewu7du7Fp0yYUFxdj4cKFmDNnDgoKCtiCIBplHA3d48eP4+mn/w/05Gwk5Zacf4MEomka3Cl5cKfkAZgNK9SOSMdxHDxWi+rqFwEAKSmpmDHjcowdOxbp6elIT0+Hz+dDXV0dMjMzkZ6ejoyMDHg88nvhROQMx0K3q6sL69b9b0RswJu/cMDlWUIImC0HEek8CYjI2b+zTAjbhOYyoOnGhR9Yc8PtGw8js2hErSLQk9KhJ10O5FwO2+yC1X0aZncD/rKnGvau3bCt8Fmff/nll6N/9nqTkenPRE52NnJycuDz+ZCUlBT98Xg8MAwDHo8HSUlJ8Hq98Hg80HUdbrc7+mMYBtxuNzRNO+vcnHmToL4jbyHEWX+2LAumaSISiUAIAV3X4XK5oOt6dP+97/W+33tMInIwdDdt2oS6upNILrwWLiMFZuthmG2HzvqMbQYgzIEfteP1elF2Yxm2bduGYHfrkI5tddUh3FQNlyFviZqRcRmMzIkX9FmXkQpXxkQYGX//vBAWRCQMYYUg7PDf/2wFYUVCaOwMoKHtFHDgCIQVhrAiEIJ3VCNyyje/+U3cd999MR8wOBa64XDPyE3TznEI2xz0V2VlZfj2t78NIQS2bNky9AJsE4C80L1YmqYDugHg65GlLgDYgLAB3QaEBSEswLbg0gGhuWBHwgxeIoc49exFx0L3nnvuQXV1DVpPfgJv4XUwMif2GwmGWw4gdGrXgNtv27YNQghs3759WMf35M6Ex39hdxqTTVhhWN0NiHSfhh1oAqwA7EgIwo4Muo3Pl47snGzk5IyHz+eD1+uNthbO/BmovdD7T3/DMKL/9Ad6+s5CiOhPb9uh92/2M1sLva972wum2fMX5pnthd4Wxpnthd73DMOApmmwbTv607td72f7jih6a+pr165duOKKKy7q/0Gi4zk6N5Xnx7HQzcjIwL/926N48KGHEDyxA8kF/9Bvna6R2ROKkY7+Pd2wZWLrHz6A5vJCT/Fd+IGjPd1JF/3fEEu2GUCkoxZW53FEuhoACLjdbkyZPBnjxo2LTqSlp6fj9OnTKCkpQUZGBjIyMpCeng5dP/cly4mIfWBKRI6uXigqKsK9//IveOaZZxBu2IekvLNXMGiaBo9/Mjz+yU6WoYwVbEWk8wSszpOwAk0AgEsumYBFFSsxZ84cTJ48ecCVCbt27cKsWbNkl0tEEji+Tvfaa6/Fe++9hy8OngCQ+MvGhBCwOk/CbN6PSHcDAKBo8mTMu+pGzJ8/HwUFBYorJCKVpFyRlpeXhy+qv5JxKGWEEIh0HIfZ9DdYwTZkZ+dg+ep/xtVXX42srCzV5RHRCCEldLOysmCZwUEnRuJZT9jWfh22rZgwYQL+6Z/WYvHixXC7pT0NiYjihJRU8Pv9gLAhrJC0e+r2ElYYQlg9S9dcesxuuiOEhUjbUZgt1bCCbRg3bjxuu+2fsWjRolE56UVEF0Ze6AIQkSAgKXSFsBFu2g+z6W8QthV9352SC7e/GG7f+GEFsBVqQ6TtKKz2I7DMbhQUFGLFiv+BxYsXM2yJ6Lwkh24AQOa5PxwDVqAZoVP/BSvYivnzF2D27FnRx/X8539+iMYTf4buSYM7ayqMzMsGDd+ey3UbYZtdEGY37GAjrGArNE3D7NmzcfPNN2POnDkJ1zIhIudI6+kCvaHrLCvQhMDR95GRkYF7/tcjmD9//lm/v+222/Dpp5+isrISNTWfI9J6AJ7cWdBTxwEQEJYJq7sekdZDiHSdim6XmpqGgssKcPWilVi0aFH0LxIioqGQOtK1I0FHjyMsE6G6ncjKysKzz25Eenp6v8/ouo6FCxdiwYIF+PTTT/HCiy+i/vifoLn0s9oQ2dk5KFu6CvPnz8fYsWORnBw/lxQT0cglJXS9Xi+8Xi8sh0M3WL8LdrgLDz30gwED90yapmH+/Pm48sor8d5776Gurg6pqalITk5GQUEBZs6cyR4tEcWctDVNmX4/Gjuday+YbUcQaTuC1atXY/r06Re8nWEYuPHGGx2ri4joTNIeW5CTnQ1hOTPStc1uhOt3Ydq0aVi5cqUjxyAiigVpoev3+6E5FLrhxi+gwcb999/PlgARjWhSQ9d2YPWCHe5EpO0QysrKMG7cuJjvn4golqSFblZWFmwrAnGOG5cPR6hxH3S3jltvvTWm+yUicoLUkS7w9VVpMdJ7dVhFeTkfbU5EcUF66MayxRBu2IekpCSsWLEiZvskInKS1PYCELuRbu8jzW++eRkyMjJisk8iIqcpaC/EZqRrddUBAK6//vqY7I+ISAZpoevz+aDreuxGul2nkTdmDHJzc2OyPyIiGaSFrqZpyMjIjElPVwgBO9iIWSWJ//gfIkos0kIXALKzs2Iy0rVDrbAjIZQwdIkozkgN3aysrJhclWZ1nQYAzJgx46L3RUQkk9TQ9fv9Mbn/gtV9GmPGjkVOTk4MqiIikkf6SLfnAZXW+T88CCFs2MEG9nOJKC5JH+kCgIiEhr0PO9QGOxLGzJkzY1UWEZE0ikJ3+CsY2M8lonimKHSH39dlP5eI4pn0ni4w/PsvCGHDDrCfS0TxS2ro9t4jYbgjXTvYCttiP5eI4pfU0DUMA2lpvmH3dK1gCwCguLg4lmUREUkjNXQBwJ/lH/5IN9wBt9uNvLy8GFdFRCSH9NC9mAdUinAHxo4dx+egEVHckh66Pp8PsMPD2zjSgfz8CbEtiIhIIumhm5qaCmENPXSFsBEJdeKSSy5xoCoiIjmUhK5tmRBCDGk7YXYBwmboElFckx66KSkpgLCBId5/wQ61AwBDl4jimvTQTUtLAwAIa2iPYrfDHQAYukQU39SMdAEIe+ihm5qWhvT0dCfKIiKSQklPF8CQVzDY4Q5M4CiXiOKcstAdansBkU5MmMDlYkQU3+KivSAsE1a4m/1cIop7CifSLry9wEk0IkoUyka6GMJI1w5zuRgRJQbpoev1eqFp2pB6una4A5qmYfz48Q5WRkTkPOmhq2kaPElJQ+rp2uEO5OTmwTAMBysjInKe9NAFAG+Sd0g9XWF2IH8CWwtEFP/UhK73wke6QgiIcAeXixFRQlASusnJyRc8kSYiAdhWhJNoRJQQlIRuUlIStAsMXS4XI6JEoqi94L3g9kKkoxaapqGgoMDhqoiInDeiQ9cKtsBsOYgbbrgBfr9fQmVERM5S1l6wI+Fz3shcCIFw/S74fD6sWbNGYnVERM5RNtIFcM7JtEjbYUS6G7F27X+PXjpMRBTvlIbuYC0GYYUQbtiDqVOn4dprr5VZGhGRo9SG7iCXAodbvoIdCeKee/4nXC4lJRIROUJZTxcAxCA3MrcCjRg//hJMnDhRZllERI5T29MdYKQrhABCLZg6tVhyVUREzhtxPV0R6YZlBjBlyhTZZREROU5te2GAka4VaAIAhi4RJSTFI93+PV0r0Azd7call14quSoiIucpCV232w3DMAYc6drBJlw28TLeO5eIEpKy9VjJySn9HsMuhA07yEk0IkpcykI3NTW130jXDrVD2BH2c4koYSkL3bS01H6rFziJRkSJTmHopvW794IdbEJKSirGjRunqCoiImcpC92UlBRoInLWeyLYjOLiKdA0TVFVRETOUtzT/ftEmrAjiATbUFzMSTQiSlxKQ9c+YyLNCjYDEOznElFCUzvStSMQwgIA2IFmAJxEI6LEpjR0gb9fCmwFmpCbm4eMjAxVJREROU7pRBqA6AoGEWrG9OnTVJVDRCTFiBjp2mY3rHAXJ9GIKOG5VR04Grp2GHagCwAYukSU8EbESNcKNMLtdvNJEUSU8JSHLmwTdrAZk4qKeGcxIkp4yifSbCsEO9iMaVOnqiqFiEgahbd2TAYAWN0NELbFfi4RjQrKQlfXdXi9ybC66gFwEo2IRgdloQt83WIQFjIz/cjJyVFZChGRFEpDt3cybdq0qbyzGBGNCkpDNy0tDQAwlZNoRDRKKB7p9qxgYD+XiEYL5SNdl8uFSZMmqSyDiEgaZZcBA8DixYsxfvx4eL1elWUQEUmjNHTnzp2LuXPnqiyBiEgqpe0FIqLRhqFLRCQRQ5eISCKGLhGRRAxdIiKJGLpERBIxdImIJGLoEhFJxNAlIpKIoUtEJBFDl4hIIoYuEZFEDF0iIokYukREEjF0iYgkYugSEUnE0CUikoihS0Qk0aCP6xFCAADC4bAjBw6FQo7sN1Hw/Jwbz8/58Rydm1PnpzczezO0L00M8puOjg7U1NQ4UhQRUaKbMmUKfD5fv/cHDV3bttHV1QXDMKBpmuMFEhElAiEETNNEamoqXK7+HdxBQ5eIiGKPE2lERBIxdImIJGLoEhFJxNAlIpKIoUtEJBFDl4hIIoYuEZFEUkN3y5YtuOmmm1BWVoZXX31V5qHjxh133IHy8nIsW7YMy5YtQ1VVleqSlOvs7ERFRQVqa2sBADt27MCSJUtQVlaGZ555RnF1I0Pfc/Twww+jrKws+j3avn274grVee6551BeXo7y8nJs2LABgOLvkJDk1KlT4pprrhEtLS2iq6tLLFmyRBw4cEDW4eOCbdti0aJFwjRN1aWMGLt37xYVFRXi8ssvF8ePHxeBQECUlpaKY8eOCdM0xdq1a8WHH36oukyl+p4jIYSoqKgQ9fX1iitT75NPPhG33nqrCIVCIhwOizVr1ogtW7Yo/Q5JG+nu2LED8+bNQ2ZmJlJSUnD99dfjD3/4g6zDx4VDhw4BANauXYulS5filVdeUVyRer/97W/x2GOPIS8vDwCwZ88eFBYWIj8/H263G0uWLBn136O+5ygQCODkyZN45JFHsGTJEjz77LOwbVtxlWrk5ubi+9//PjweDwzDwKRJk3DkyBGl36FB7zIWa6dPn0Zubm70dV5eHvbs2SPr8HGhvb0d8+fPxw9+8AOYpok1a9Zg4sSJWLhwoerSlPnxj3981uuBvkf19fWyyxpR+p6jxsZGzJs3D4899hh8Ph/uvvtubN68GStXrlRUoTqTJ0+O/vnIkSN49913cfvttyv9Dkkb6dq2fdaNc4QQvJFOH3PmzMGGDRvg8/mQlZWFFStW4KOPPlJd1ojC79H55efn42c/+xny8vKQnJyMO+64Y9R/jw4cOIC1a9fioYceQn5+vtLvkLTQHTt2LBoaGqKvGxoaov8coh6ff/45du7cGX0thIDbLe0fI3GB36Pzq66uxh//+Mfo69H+Pdq1axfuvPNOPPDAA1i+fLny75C00F2wYAF27tyJ5uZmBAIBbNu2DYsXL5Z1+LjQ0dGBDRs2IBQKobOzE2+88Qauu+461WWNKLNmzcLhw4dx9OhRWJaFrVu38nvUhxACTzzxBNra2mCaJl577bVR+z2qq6vDd7/7XTz11FMoLy8HoP47JO2vvzFjxuD+++/HmjVrYJomVqxYgZKSElmHjwvXXHMNqqqqcPPNN8O2baxevRpz5sxRXdaIkpSUhPXr1+Pee+9FKBRCaWkpbrjhBtVljShTp07FXXfdhVWrViESiaCsrAwVFRWqy1LihRdeQCgUwvr166Pvfetb31L6HeL9dImIJOIVaUREEjF0iYgkYugSEUnE0CUikoihS0QkEUOXLkptbS2mTZsWvZvVsmXLsHTpUmzevHnQbTo6OrBmzZphH7OyshJ33333sLcnUmn0XqZCMeP1evHWW29FX9fX16OiogIzZszA1KlT+32+ra0Ne/fulVki0YjB0KWYGzNmDAoLC3HkyBF89NFHeOONN+B2u1FYWIj169fj4YcfRjAYxLJly1BZWQld1wfd1y9+8Yt+2wM9l27eddddqKurg67rePrppzFp0iTs3r0bTz75JMLhMBoaGrBgwQI88cQTqK2txZ133onS0lJUVVWhvb0dDz74IK677joEAgE89thjqKqqgs/nQ1FREQBg/fr1qK+vx+OPP466ujqYpony8nJ85zvfkXIeKTGxvUAx99e//hXHjh1DIBBAZWUlXnvtNWzduhUTJkzAK6+8gp/85CfR0fG5Avf9998fcHsAOH78OB599FFs2bIFV155JV544QUAwMsvv4z77rsPv/vd7/DOO+/ggw8+wL59+6LbLFq0CJs3b8YDDzyAJ554AgDw85//HJZl4d1338VLL72EL774IlrDgw8+iFtuuQWVlZXYvHkzduzYgd///vdOnToaBTjSpYvWO2oFAMuy4Pf78eSTT+Ljjz/GDTfcgIyMDAA9TzMAEH26wfns3LlzwO0rKytRUlKCwsJCAMC0adOiT0ZYv349/vSnP2HTpk04dOgQQqEQuru7kZmZCcMwUFpaCgCYPn06WltbAQAfffQRHn74YbhcLqSlpWH58uWorq5Gd3c3PvvsM7S1tWHjxo0AgO7ubuzfvx833XTTRZ83Gp0YunTR+vZ0e+3YseOsW+a1t7ejvb39gver6/qg25951yxN09B7Nfvtt9+O4uJiXH311bjxxhtRVVUV/Z1hGHC5XNFterndbpx5NXzvZ2zbhhACv/nNb5CcnAwAaG5uRlJS0gX/NxD1xfYCOWbBggXYvn07Ojs7AQA//elP8dJLL8HtdsOyLJzvth+DbT+Y9vZ27N27F9/73vdQVlaGU6dO4dixY+d9akJpaSlef/112LaNQCCArVu3QtM0pKWlYfbs2fjlL38Z3f+qVavw/vvvD+EsEJ2NI11yTGlpKQ4ePIhVq1YBAIqKirBu3TokJyejpKQE5eXlePXVV+H3+4e0/bZt2wb8fHp6Ou666y4sX74cKSkpGDNmDL7xjW/g6NGjyM/PH7TOu+++G48//jiWLFkCn8+H7OxseL1eAMBTTz2FdevWYcmSJQiHw6ioqMDSpUsv5rTQKMe7jNGo98477yAtLQ2lpaWwbRv33nsvFi5ciNWrV6sujRIQQ5eUWr16Nbq6ugb83auvvoq0tDTHa6ipqcEPf/hDBAIBmKaJq666Co888ggMw3D82DT6MHSJiCTiRBoRkUQMXSIiiRi6REQSMXSJiCRi6BIRScTQJSKS6P8Dt3LuQ4/4t5kAAAAASUVORK5CYII=%0A" width="200" />
</p>
```































