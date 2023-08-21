[back](https://bandytwin.github.io/)

## ICR - Identify Age Related Conditions
[Kaggle Competition](https://www.kaggle.com/competitions/icr-identify-age-related-conditions)

I used Python for this competition, and my final classification [solution](https://www.kaggle.com/code/abandura/mbd-forecasting-w-mars-lms-r) scored in the top 12% in terms of accuarcy from over 6,400 teams.

**Project description:** "The goal of this competition is to predict if a person has any of three medical conditions. You are being asked to predict if the person has one or more of any of the three medical conditions (Class 1), or none of the three medical conditions (Class 0). You will create a model trained on measurements of health characteristics.

To determine if someone has these medical conditions requires a long and intrusive process to collect information from patients. With predictive models, we can shorten this process and keep patient details private by collecting key characteristics relative to the conditions, then encoding these characteristics.

Your work will help researchers discover the relationship between measurements of certain characteristics and potential patient conditions." - Kaggle Description

### 1. Data Overview and Evaluation Metric

For each observation, we are provided with 56 anaonymized health characteristics (likely including things like age, cellular property measurements, etc.) of the patient as well as a binary label for whether or not they have a health-related illness. Additional data for the training samples only is also provided, which contains the date that the observation was made, three experimental characteristics, and identifiers for which of 3 specific disease the patient has if they are sick. While this supplementary data is not available for the test data set, it is still essential to model training.

The evaluation metric used for this competition is [balanced logarithmic loss (BLL)](https://www.kaggle.com/competitions/icr-identify-age-related-conditions/overview/evaluation). Because the data is fairly unbalanced, containing 5x as many observations for healthy individuals as sick ones, it's important to use a metric that balances performance between the classes. BLL also exponentially penalizes false confident misclassifications (see the plot below). This behavior is especially beneficial in medical diagnosis applications: Confidently 

<img src="images/smape_formula.png?raw=true"/>

### 2. Exploratory Data Analysis

The data contains two classes of counties that are difficult to model and forecast efficiently: 
- Counties with very few microbusinesses and therefor low, noisy MD.
- Counties with discontinuities (see the plot below for some examples).

<img src="images/levelshift_ex.png?raw=true"/>

The source of these discontinuities is nebulous, but if they are included in modeling they degrade overall performance. An effective way to ID them is by selecting counties with a month-over-month absolute change in MD greater than 25%. This method has the added benefit of also catching the counties with the smallest MD values. Due to time constraints, I chose to exclude the difficult-to-model counties from the main modeling process, and simply use persistence forecasts for them. That said, I think creating a process to remove discontinunities has a lot of potential to improve performance.

<img src="images/cfips_dist.png?raw=true"/>

### 4. CV Strategy


### 3. Methods That Weren't Successful

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

### 4. Final Modeling Approach and Performance

<img src="images/mb_sample_fc.png?raw=true"/>


