[back](https://bandytwin.github.io/)

## Parkinson's Freezing of Gait (FOG) Prediction
[Kaggle Competition](https://www.kaggle.com/competitions/tlvmc-parkinsons-freezing-gait-prediction)

**Project description:** "The goal of this competition is to detect freezing of gait (FOG), a debilitating symptom that afflicts many people with Parkinson’s disease. You will develop a machine learning model trained on data collected from a wearable 3D lower back sensor.
Your work will help researchers better understand when and why FOG episodes occur. This will improve the ability of medical professionals to optimally evaluate, monitor, and ultimately, prevent FOG events."

I used PyTorch for this competition, and my final prediction model [solution](https://www.kaggle.com/code/abandura/fog-1d-cnn) scored in the top 4% in terms of accuarcy from over 1,300 teams.

### 1. Data Overview and Evaluation Metric

The data provided is the raw output from 3D accelerometers worn by patients on their lower backs as they completed FOG inducing protocols. These sessions were video recorderd as well, and experts review to footage so they could label timestamps where FOG occured. An example of this labeled acceleromtered data is shown below, with the dark grey band representing the presence of a FOG event.

<img src="images/fog_sample.png?raw=true"/>

Because three distinct types of FOG events exist in the dataset ("start hesitation", "turn", and "walking"), model performance was measured with the mean [absolute precision](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html) across the three classes. This encourages balanced classification performance for each class, and requires a modeling approach that can handle imbalanced classes since one of the FOG types was much more common in the dataset than the other two.

### 2. Data Pipeline Overview



### 3. CNN Model Architecture

I test a number of different modeling approaches, including traditional forecasting methods (ARIMA, ETS, TBATS, etc.) as well as tree-based methods (XGboost, lightGBM), however the most accurate model endeded being an autoregressive linear model trained to minimize SMAPE. The features I used were lagged difference and moving average terms, along with an ETS forecast of future MD, and solved the model weights by minimizing the following objective function:

<img src="images/fog_cnn_arch.jpeg?raw=true"/>

### 4. Results

<img src="images/mb_sample_fc.png?raw=true"/>


