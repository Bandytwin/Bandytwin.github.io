[back](https://bandytwin.github.io/)

## GoDaddy - Microbusiness Density Forecasting 
[Kaggle Competition](https://www.kaggle.com/competitions/godaddy-microbusiness-density-forecasting)

**Project description:** The goal of this competition is to forecast monthly microbusiness density (MD) over the next 6 months for county in the U.S. This work will help policymakers gain visibility into microbusinesses, a growing trend of very small entities. Additional information will enable new policies and programs to improve the success and impact of these smallest of businesses.

I used R for this competition, and my final forecasting [solution](https://www.kaggle.com/code/abandura/mbd-forecasting-w-mars-lms-r) scored in the top 4% in terms of accuarcy from over 3,500 teams.

### 1. Data Overview and Evaluation Metric

Looking at average MD for all counties, the strongest observable feature present is linear growth over time. Additionaly, there does not appear to be much seasonality present other than a slight dip in winter, though with only a few years of data it's difficult to know for certain.

<img src="images/mb_avg.png?raw=true"/>

The evaluation metric used for this competition is symmetric mean absolute percent error ([SMAPE](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)). SMAPE is based on relative errors, which means that counties with smaller MB densities are over-emphasized. Indeed the baseline persistence forecast method scored quite well, and was especially difficult to beat for shorter forecasting horizons. Success in this competition required either ransforming MD to a relative value (i.e. with a rolling division) to use as a target for model training, or to choose a methodology that more directly minimizes percent errors.

<img src="images/smape_formula.png?raw=true"/>

### 2. Outlier Detection

The data contains two classes of counties that are difficult to model and forecast efficiently: 
- Counties with very few microbusinesses and therefor low, noisy MD.
- Counties with discontinuities (see the plot below for some examples).

<img src="images/levelshift_ex.png?raw=true"/>

The source of these discontinuities is nebulous, but if they are included in modeling they degrade overall performance. An effective way to ID them is with a month-over-month absolute change in MD over 25%, and has the added benefit of also catching the counties with the smallest MD values. Due to time constraints, I chose to exclude the difficult-to-model counties from the main modeling process, and simply use persistence forecasts for them. That said, I think creating a process to remove discontinunities has a lot of potential to improve performance.

<img src="images/cfips_dist.png?raw=true"/>

### 3. Linear Model Minimizing SMAPE

I test a number of different modeling approaches, including traditional forecasting methods (ARIMA, ETS, TBATS, etc.) as well as tree-based methods (XGboost, lightGBM), however the most accurate model endeded being an autoregressive linear model trained to minimize SMAPE. The features I used were lagged difference and moving average terms, along with an ETS forecast of future MD, and solved the model weights by minimizing the following objective function:

```R
# objective function for linear model optimization
fn = function(par, df, r_cols, y, norm_col=F, l2=0, l1=0) {
  preds = rep(par[1], df[,.N])
  l2p = 0
  l1p = 0
  for (ind in 2:length(par)) {
    i_col = r_cols[ind-1]
    preds = preds + par[ind]*df[, (get(i_col)-mean(get(i_col)))/sd(get(i_col))]
    l2p = l2p + l2*(par[ind])^2
    l1p = l1p + l1*abs(par[ind])
  }
  err = smape(a=df[,get(y)], fc=preds) + l1p + l2p
  return(err)
}
```

### 4. Results

<img src="images/mb_sample_fc.png?raw=true"/>

