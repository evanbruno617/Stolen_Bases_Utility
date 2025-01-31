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

For the accuracy in the utility equation I replaced that value with the validation accuracy for this model which was 92%.

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/rf_graph.png)

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/rf_desc.png)

Filtering when the utility was above -1 had a gain of over 300 rbis while under -1 had a gain of only 75 rbis. In order to find the best posssible threshold a test was run by iterating through the values in steps of 0.1. 

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/threshold_rf.png)

Now having the threshold be above -0.3 seem to maximize the expected gain of rbis. When that number increase from there on out it seems that number decreases unitl you get a negative return. The second to last of the right columns represents the return of rbis given the expected rbis / total number of observations. The last column represents the total rbis from staying / total number of observations. As you can see the percentage of stay rbis were slowly decreasing as well until it jumps past that threshold where the expected gain of rbis turned negative. 

### Deep Model

For the accuracy in the utility equation I replaced that value with the validation accuracy for this model which was 88%.

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/deep_graph.png)

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/deep_desc.png)

Filtering when the utility was above -1 had a gain of 273 rbis while under -1 had a gain of only 72 rbis. In order to find the best posssible threshold a test was run by iterating through the values in steps of 0.1. 

![image](https://github.com/evanbruno617/Stolen_Bases_Utility/blob/main/Photos/threshold_deep.png)

Now having the threshold be above -0.1 seem to maximize the expected gain of rbis. When that number increase from there on out it seems that number decreases unitl you get a negative return. As you can see the percentage of stay rbis were slowly decreasing again until it jumps past that threshold where the expected gain of rbis turned negative. 

# Conclusion
---
The undeniable success of this project was the ability to predict the chance a runner would be able to steal a base. This can be valuable information for managers to use when a runner is on base and they're thinking about sending them to 3rd base. By using this model they can predict the chance of them being successful with a model accuracy of 92% assuring that the predictions are accurate. For the expected utility there is much work to be done in terms of constructing the expected utility and determining the optimal threshold limit. There are many different factors that could go into constructing what the utility is and will continue on researching and finding new variables to add and tweak to input into the model. For regarding the threshold next I will test within an interval rather than being greater than or less than a certain value. By capturing and testing intervals I believe this may better capture the best gains in expected rbis gained. 




