[back](https://bandytwin.github.io/)

## ICR - Identify Age Related Conditions
[Kaggle Competition](https://www.kaggle.com/competitions/icr-identify-age-related-conditions)

**I used Python for this competition, and my final classification [solution](https://www.kaggle.com/code/abandura/icr-inference-final) scored in the top 12% in terms of accuarcy from over 6,400 teams.**

**Project description:** The goal of this competition is to predict whether a person is healthy (class 0) or has one of three age-related medical conditions (class 1) using a model trained on measurements of various health characteristics.

The diagnostic process for these medical conditions requires a long and intrusive process to collect information from patients. With a predictive model this process can be shortened and anonymized, which greatly reduces costs and inconvenience to patients.

### 1. Data Overview and Evaluation Metric

For each observation, we are provided with 56 anaonymized health characteristics (likely including things like age, cellular property measurements, etc.) of the patient as well as a binary label for whether or not they have a health-related illness. Additional data for the training samples only is also provided, which contains the date that the observation was made, three experimental characteristics, and identifiers for which of 3 specific disease the patient has if they are sick. While this supplementary data is not available for the test data set, it is still essential to model training.

The evaluation metric used for this competition is [balanced logarithmic loss (BLL)](https://www.kaggle.com/competitions/icr-identify-age-related-conditions/overview/evaluation). Because the data is fairly unbalanced, containing 5x as many observations for healthy individuals as sick ones, it's important to use a metric that balances performance between the classes. BLL also exponentially penalizes false confident misclassifications (see the plot below). This behavior is especially beneficial in medical diagnosis applications: Confidently labeling a sick person as healthy could delay them receiving essential treatment, and confidently labeling a healthy person as sick could result in unecessary and expensive testing. The behavior of BLL across the prediction region is shown in the plot below:

<img src="images/icr-age/bll_ex.png?raw=true"/>

### 2. Exploratory Data Analysis

Because the dataset only contains 617 samples (which is quite small for 56 features), the first step I took after handling missing values was to investigate colinearity between features. Looking at the feature pairwise correlations, plotted below, it turns out the features are fairly uncorrelated other than a few spefic pairs. I removed one feature because it was redundant (perfectly predicted by a combination of other features), but other than that I had difficulty improving accuracy with both feature removal and dimensionality reduction (PCA, LLE, etc.) as well as by removing features with least model importance. I think this difficulty, combined with the lack of colinearity, speaks to the information density described the original features.

<img src="images/icr-age/feature_cor.png?raw=true"/>

I also used t-SNE to visualize the dataset in 2D, and to help get an idea of how seperable to two classes were. What's interesting is there are clearly samples that will be difficult to classify because they are surround by samples of the opposite. 

<img src="images/icr-age/tSNE_2.png?raw=true"/>

### 4. CV Strategy



### 3. Methods That Weren't Successful

<img src="images/icr-age/cos_sim.png?raw=true"/>

### 4. Final Modeling Approach and Performance



<img src="images/icr-age/icr_performance.png?raw=true"/>


