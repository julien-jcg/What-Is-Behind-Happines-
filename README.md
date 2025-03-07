# What-Is-Behind-Happiness-?
Author: Julien Jonquères Cauquil-Gleizes

## Project Overview
This project aims to reply to the research question "**What is behind happiness?**" and its subquestion "**What makes a country happy?**. To address these questions, this project analyzes the relationship between happiness scores and various socio-economic factors using a **panel fixed-effects regression model**. Due to high multicollinearity among explanatory variables, **Principal Component Analysis (PCA)** was applied to reduce dimensionality while preserving meaningful information.

## Data Description
The dataset covers 8 observations of 199 countries over the 2015-2020 period (the period ends in 2020 because no data was available for the air pollution after 2020).
The dataset includes:
- **HappinessScore** (the dependent variable), which measures countries' happiness levels. It comes from the 2015-2020 World Happiness Reports (<https://www.kaggle.com/datasets/sazidthe1/global-happiness-scores-and-factors>).
- **PerCapitaGDP**, which is the GDP per capita of each country for each year. The data comes from the World Bank.
- **LifeExpectancy**, wich measures the average life expectancy of each country for each year. The data comes from the World Bank.
- **SocialSupport**, which is the national average of the binary responses (0=no, 1=yes) to the Gallup World Poll (GWP) question “If you were in trouble, do you have relatives or friends you can count on to help you whenever you need them, or not?”.
- **Freedom**, which stands for freedom to make life choices, is the national average of binary responses to the GWP question “Are you satisfied or dissatisfied with your freedom to choose what you do with your life?”.
- **Corruption**, which stands for perceptions of corruption, is the average of binary answers to two GWP questions: “Is corruption widespread throughout the government or not?” and “Is corruption widespread within businesses or not?” (where data for government corruption are missing, the perception of business corruption is used as the overall corruption-perception measure).
- **AirPollution**, which is PM2.5 air pollution, mean annual exposure (micrograms per cubic meter). The data comes from World Bank's World Development Indicators.
- **GenderInequality**, which stands for gender inequality index (GII), is a composite metric of gender inequality using three dimensions: reproductive health, empowerment and the labour market. A low GII value indicates low inequality between women and men, and vice-versa.


## Project's Outline
### 1. **Analyzing the Dataset**
- **Correlation Plot** to analyze the realtionships between happiness and the proposed explanatory variables.
- **Log-transformation** of Per Capita GDP and Air Pollution to normalize the data:
```python
happinessdata["LogPerCapitaGDP"] = np.log(df["PerCapitaGDP"])
happinessdata["LogAirPollution"] = np.log(df["AirPollution"])
```

### 2. **First Fixed-Effect Regression Model**
The first fixed-effects regression model is run with:
- The **dependent variable**: `HappinessScore`.
- The **independent variables**: `LogPerCapitaGDP`, `LifeExpectancy`, `SocialSupport`, `Freedom`, `Corruption`, `LogAirPollution`, and `GenderInequality`.
Why a fixed-effects regression model? To control for time-invariant differences across countries.
```python
model1 = smf.ols(
    formula = "HappinessScore ~ LogPerCapitaGDP + LifeExpectancy + SocialSupport + Freedom + Corruption + LogAirPollution + GenderInequality + C(Country)",
    data = happinessdata
).fit()
```

### 3. **Multicollinearity Check (VIF Analysis)**
The **Variance Inflation Factor (VIF)** was calculated for all explanatory variables.
```python
vif_data = pd.DataFrame()
vif_data["Variable"] = ["LogPerCapitaGDP", "LifeExpectancy", "SocialSupport", "Freedom", "Corruption", "LogAirPollution", "GenderInequality"]
vif_data["VIF"] = [variance_inflation_factor(happinessdata[["LogPerCapitaGDP", "LifeExpectancy", "SocialSupport", "Freedom", "Corruption", "LogAirPollution", "GenderInequality"]].values, i) for i in range(len(["LogPerCapitaGDP", "LifeExpectancy", "SocialSupport", "Freedom", "Corruption", "LogAirPollution", "GenderInequality"]))]
```
Since all VIF values (except Corruption) were **above 10**, indicating high multicollinearity, Principal Component Analysis (PCA) was used to extract independent components.

### 4. **Principal Component Analysis (PCA) and Second Fixed-Effects Regression Model**
- The idea was to standardize the explanatory variables (except Corruption) and extract **two principal components (PC1 & PC2)**.
- The second fixed-effects regression model was run with:
  - **Dependent Variable**: `HappinessScore`.
  - **Independent Variables**: `PC1`, `PC2`, and `Corruption`.
```python
model2 = smf.ols(
    formula = "HappinessScore ~ PC1 + PC2 + Corruption + C(Country)",
    data = happinessdata
).fit()
```

### 5. **VIF Analysis**
Another VIV analysis was carried out to assess the multicolinearity between `PC1`, `PC2`, and `Corruption`.

### 6. **Dashboard**
In order to easily visualize all the data processed, a dashboard was created. On this dashboard, you can display a map which shows each variable of every countries covered for each year (from 2015 to 2020).

## How to Use the Code
Install required Python libraries:
```bash
pip install pandas numpy statsmodels scikit-learn seaborn dash plotly
```
