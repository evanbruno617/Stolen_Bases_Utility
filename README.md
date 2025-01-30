# Stolen_Bases_Utility

## Purpose
---

The purpose of this project is to calculate the probability of a runner stealing a base. Then by using that probability calculate the expected utility that would occur given the expected utilitites of all other possibilities as well as added weights. By calculating an optimal threshold users can plan whether that time is an optimal time to steal given the current circumstances.

---

## Data
---

The data was extracted via mlb api data. During this extraction all of the stolen base data was taken as well as at bat data for analysis. The stolen base data was taken in order to aid in prediction if a player can successsfully steal a base or not. The atbat data was crucial for determining likelihood of batter fufillling the necessary duties of certain circumstances as well as gathering runner data on bases for future scenarios. When the at bat data was cleaned it included all the attributes needed for the utility model as well as what players were on base and what their position and id are (important because need their id to make predictions on probability of them stealing successfully).

### Scraping

The stolen base data provided good insights into whether or not the runner successfully stole the base however for analysis more variables were needed. Attributes such as catcher arm strength and runner speed needed to be known to aid in the accuracy. Data was [scraped](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/scraper.ipynb) on baseball savant to gather this information. It is recorded in a yearly basis so for the analysis it took into account the past year's recorded data. 

---

## Analysis

### Models for Stolen Bases

The models used for predicting if the runner would steal the base successfully were a random forest classification model and a nueral network. When running these tests it was clear their could be some overfitting so it was also validated on out of sample data ( 2024 years data ). 

For the random forest model the out of sample accuracy came in at 94% with a sufficicent recall of 92%.

![RF](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/rf_validation_acc.png)

For the neural network model the out of sample accuracy came in at 88% with a sufficicent recall of 88%.

![RF](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/deep_valid_acc.png)

These models will be used for predicting if the runner will steal or not in the upcoming analysis. 


