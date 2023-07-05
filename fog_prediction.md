[back](https://bandytwin.github.io/)

## Parkinson's Freezing of Gait (FOG) Prediction
[Kaggle Competition](https://www.kaggle.com/competitions/tlvmc-parkinsons-freezing-gait-prediction)

**Project description:** "The goal of this competition is to detect freezing of gait (FOG), a debilitating symptom that afflicts many people with Parkinson’s disease. You will develop a machine learning model trained on data collected from a wearable 3D lower back sensor.
Your work will help researchers better understand when and why FOG episodes occur. This will improve the ability of medical professionals to optimally evaluate, monitor, and ultimately, prevent FOG events."

I used PyTorch for this competition, and my final prediction model [solution](https://www.kaggle.com/code/abandura/fog-1d-cnn) scored in the top 4% in terms of accuarcy from over 1,300 teams.

### 1. Data Overview and Evaluation Metric

The data provided is the raw output from 3D accelerometers worn by patients on their lower backs as they completed FOG inducing protocols. These sessions were video recorderd as well, and experts review to footage so they could label timestamps where FOG occured. An example of this labeled acceleromtered data is shown below, with the dark grey band representing the presence of FOG.

<img src="images/fog_sample.png?raw=true"/>

The evaluation metric used for this competition is symmetric mean absolute percent error ([SMAPE](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)). SMAPE is based on relative errors, which means that counties with smaller MB densities are over-emphasized. Indeed the baseline persistence forecast method scored quite well, and was especially difficult to beat for shorter forecasting horizons. Success in this competition required either ransforming MD to a relative value (i.e. with a rolling division) to use as a target for model training, or to choose a methodology that more directly minimizes percent errors.

<img src="images/smape_formula.png?raw=true"/>

### 2. Data Pipeline Overview

The data contains two classes of counties that are difficult to model and forecast efficiently: 
- Counties with very few microbusinesses and therefor low, noisy MD.
- Counties with discontinuities (see the plot below for some examples).



The source of these discontinuities is nebulous, but if they are included in modeling they degrade overall performance. An effective way to ID them is by selecting counties with a month-over-month absolute change in MD greater than 25%. This method has the added benefit of also catching the counties with the smallest MD values. Due to time constraints, I chose to exclude the difficult-to-model counties from the main modeling process, and simply use persistence forecasts for them. That said, I think creating a process to remove discontinunities has a lot of potential to improve performance.

<img src="images/cfips_dist.png?raw=true"/>

### 3. CNN Model Architecture

I test a number of different modeling approaches, including traditional forecasting methods (ARIMA, ETS, TBATS, etc.) as well as tree-based methods (XGboost, lightGBM), however the most accurate model endeded being an autoregressive linear model trained to minimize SMAPE. The features I used were lagged difference and moving average terms, along with an ETS forecast of future MD, and solved the model weights by minimizing the following objective function:

<img src="images/fog_cnn_arch.jpeg?raw=true"/>

### 4. Results

<img src="images/mb_sample_fc.png?raw=true"/>


