# What-Is-Behind-Happines-?
Python project for PSE
# README: Happiness Score Regression Analysis

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
The regression model is run with:
- The **dependent variable**: `HappinessScore`,
- The **independent variables**: `LogPerCapitaGDP`, `LifeExpectancy`, `SocialSupport`, `Freedom`, Corruption`, `LogAirPollution`, and `GenderInequality`.

### 3. **Multicollinearity Check (VIF Analysis)**
The **Variance Inflation Factor (VIF)** was calculated for all explanatory variables.
- Since all VIF values (except Corruption) were **above 10**, indicating high multicollinearity, PCA was used to extract independent components.

### 4. **Principal Component Analysis (PCA)**
- Standardized the explanatory variables (except Corruption).
- Extracted **two principal components (PC1 & PC2)**.
- PCA Loadings were computed to understand the contribution of original variables to PC1 & PC2.

### 5. **Second Fixed-Effects Regression Model**
- The regression model was run with:
  - **Dependent Variable**: `HappinessScore`
  - **Independent Variables**: `PC1`, `PC2`, and `Corruption`
  - **Fixed Effects**: `Country` to control for time-invariant differences across countries.

### 6. **Visualization**
- A **correlation plot** was created to show the relationship between `HappinessScore` and `LogPerCapitaGDP`.
- A **dashboard with an interactive world map** was implemented to visualize country-level data over time.

## Results Interpretation
- The **PCA loadings** help interpret the effects of individual variables on happiness.
- The **fixed-effects model** accounts for country-specific variations and provides more reliable estimates.
- The **dashboard and correlation plots** give a visual understanding of happiness determinants.

## How to Use the Code
1. Install required Python libraries:
   ```bash
   pip install pandas numpy statsmodels scikit-learn seaborn dash plotly
   ```
2. Run the Python script to:
   - Perform PCA and regression analysis
   - Display VIF values
   - Generate correlation plots
   - Launch the interactive dashboard

## Conclusion
This project demonstrates the impact of economic and social factors on happiness while addressing multicollinearity using PCA. The combination of **fixed-effects regression** and **interactive visualization** provides a comprehensive understanding of global happiness trends.

