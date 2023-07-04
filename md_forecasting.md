## GoDaddy - Microbusiness Density Forecasting 
[Kaggle Competition](https://www.kaggle.com/competitions/godaddy-microbusiness-density-forecasting)

**Project description:** The goal of this competition is to forecast monthly microbusiness density over the next 6 months for county in the U.S. This work will help policymakers gain visibility into microbusinesses, a growing trend of very small entities. Additional information will enable new policies and programs to improve the success and impact of these smallest of businesses.

I used R for this competition, and my final forecasting [solution](https://www.kaggle.com/code/abandura/mbd-forecasting-w-mars-lms-r) scored in the top 4% in terms of accuarcy from over 3,500 teams.

### 1. Data Overview and Evaluation Metric

Looking at the average microbusiness density

<img src="images/mb_avg.png?raw=true"/>

The evaluation metric used for this competition is symmetric mean absolute percent error ([SMAPE](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)). SMAPE is based on relative errors, which means that counties with smaller MB densities are over-emphasized. Indeed the baseline persistence forecast method scored quite well, and was especially difficult to beat 

### 2. Outlier Detection

```javascript
if (isAwesome){
  return true
}
```

### 3. Linear Model Minimizing SMAPE

```javascript
if (isAwesome){
  return true
}
```

### 4. Results

<img src="images/mb_sample_fc.png?raw=true"/>

