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

The atbat data for these exeriments focused on all of the 30 previous atbats of that batter with the pitcher with the same hand and what their outcomes were. For this data it is focused on situations when the runner is on second. Then when calculating the probability of batter hitting rbi depends from two situations, when the runner is at second and when the runner is at third. Rbi occurence when the runner is at second consists of hits such as single, double, home run and triple. The 3rd base rbi outcome actually excludes these outcomes and only includes one that can only happen at third. This is done because the goal is to see potential rbis gained and if they go to third and they get those hits they would've got the rbi without risking the out anyway. 

Next thing introduced is this table of probability of scoring given the conditions of the game. This table was extracted [online](https://library.fangraphs.com/misc/re24/) and will be used in calculating utility. 

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/chart.png)

#### Utility

For calculating the utility there are 3 different parts being considered, utility of stealing, utility of being caught, and the utility of staying. 

- The utility of stealing is calculated by the (past success of stealing) * (past success of batter hitting rbi at third) * (chart with current base at next base)
- The utility of being caught is calculated by the (1 - past success of stealing) * (chart with simulated out - chart with current outs at current base) (this shows loss in probability or utlity if caught stealing)
- The utility of staying is calculated by the (prob of staying) * (chart with the current outs at current base)

#### Weights

The equation that will calculate the total expected utlity is utility steal - utility caught - utility stay however need to add weights to them in order to empahsize certain situations to steal. 

With weights the new utility is - Utility = utility steal * diff in score (distribution so that the closer the score has a higher weight) * number of outs left in inning (neg exponential so that higher values have less weight) - utility caught * inning * current outs (positive exponential so that higher number has higher weight) - utility stay * current outs (neg exponential).

## Models
---
### Lookback
This model takes a look at the accuracy of a runner stealing a base by looking at their past 10 stolen base attempts and how they went. The following is the utility graph and the results.

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/lookback_graph.png)

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/lookback_desc.png)

In this case an arbritrary amount of -1 utility was the threshold to compare. The expected return of RBIs is calculated by total rbi outcomes at 3rd base times the probability of success. However the expected gain of rbi is the expected return minus the rbi occured if they stayed at the base. This represents how many more rbis they would have gotten if they attempted to steal rather than staying on base. 

### RF Model

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/rf_graph.png)

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/rf_desc.png)




