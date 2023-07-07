[back](https://bandytwin.github.io/)

## Parkinson's Freezing of Gait (FOG) Prediction
[Kaggle Competition](https://www.kaggle.com/competitions/tlvmc-parkinsons-freezing-gait-prediction)

**Project description:** "The goal of this competition is to detect freezing of gait (FOG), a debilitating symptom that afflicts many people with Parkinson’s disease. You will develop a machine learning model trained on data collected from a wearable 3D lower back sensor.
Your work will help researchers better understand when and why FOG episodes occur. This will improve the ability of medical professionals to optimally evaluate, monitor, and ultimately, prevent FOG events."

I used PyTorch for this competition, and my final prediction model [solution](https://www.kaggle.com/code/abandura/fog-1d-cnn) scored in the top 4% in terms of accuarcy from over 1,300 teams.

### 1. Data Overview and Evaluation Metric

The data provided is the raw output from 3D accelerometers worn by patients on their lower backs as they completed FOG inducing protocols. These sessions were video recorderd as well, and experts review to footage so they could label timestamps where FOG occured. An example of this labeled acceleromtered data is shown below, with the dark grey band representing the presence of a FOG event.

<img src="images/fog_sample.png?raw=true"/>

Because three distinct types of FOG events exist in the dataset ("start hesitation", "turn", and "walking"), model performance was measured with the mean [absolute precision](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html) (mAP) across the three classes. This encourages balanced classification performance for each class, and requires a modeling approach that can handle imbalanced classes since one of the FOG types was much more common in the dataset than the other two.

### 2. Data Pipeline Overview

I approached this problem as a segment, 4-class classification problem (3 FOG types, and the absence of FOG as a fourth class), which means feeding the CNN a segment of 1-2 seconds of data and classifying a timestep near the center of the segment. The dataloader performed feature engineering on each patient data file when initially loading it, and then windowed the data for each batch during training in order to accomodate the limited RAM resources. I tested a lot of different spectral and temporal features generated from the raw accelerometer data, but wasn't able to improve performance of the accelerometer data on its own.

### 3. CNN Model Architecture

I used 6-fold CV to perform hyperparameter tuning, which led to the final model architecture summarize in the figure below, and also includes batch normalization layers between every convolutional layer. The final mAP was 0.30.

<img src="images/fog_cnn_arch.jpeg?raw=true"/>

### 4. Example Class predictions

<img src="images/fog_preds.png?raw=true"/>


