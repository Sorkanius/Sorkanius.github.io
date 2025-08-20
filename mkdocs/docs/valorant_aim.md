---
Author: Ignacio Peletier
Title: VALORANT AIM
---

VALORANT is a free-to-play tactical first-person shooter by Riot Games. It launched in June 2020. I designed a small experiment to analyze aim performance. The study covers weapon accuracy, distance effects, and player improvement over time.  

Key questions:

* Which weapon gives me better results?  
* Does distance reduce the number of bots I can eliminate?  
* Do weapons perform differently at close vs. long range?  
* Do I improve as the rounds progress?  

---

## Experiment Setup  

In the practice range, 30 static bots were used. Each had armor (150 HP). Difficulty was set to **medium**.  

Two distances were tested:  

![Close Distance](imgs_va/vandal_close.png "Close Distance")  
*Close distance*  

![Long Distance](imgs_va/vandal_long.png "Long Distance")  
*Long distance*  

Weapons: Sheriff, Phantom, and Vandal.  

For every configuration, I recorded 10 trials:  
`3 weapons x 2 distances x 10 measurements = 60 samples`.  
The order was randomized to reduce bias.  

---

## Data Visualization  

The distribution of bots eliminated per weapon and distance:  

![downed bots distribution](imgs_va/distribution.png "Bots downed per weapon and distance")  
*Distribution of downed bots in different setups*  

Results show clear differences. The Vandal outperformed the Sheriff and Phantom. Later analysis checks if these differences are statistically significant.  

Weapon × distance interaction was also tested. Does accuracy drop less for some weapons at longer range?  

![downed bots interaction](imgs_va/interaction.png "Interaction between weapon and distance")  
*Weapon × distance interaction*  

Findings: accuracy dropped at long range. The vandal is better at both distances. The Phantom and Sheriff interaction is interesting: the Phantom was stronger at close range but both are similar at longer range.  

I also tracked accuracy over time:  

![downed bots evolution](imgs_va/evolution.png "Evolution of downed bots")  
*Performance across rounds*  

Later tests check if this trend was statistically different from zero.  

---

## Analysis  

I used a **two-way ANCOVA** (Analysis of Covariance) with `weapon`, `distance`, and `round`.  

### Linearity Assumption

The linearity is assessed by visual inspection of the covariate (`round`) for each available group:

![downed bots evolution](imgs_va/individual_plots.png "Evolution of downed bots")*Linearity assumption plot*

The smoothing method used was *loess* (locally weighted smoothing). The plots are more or less linear, with some exceptions.

### Homogeneity of Regression Slopes

In order to proceed with the ANCOVA, it is important to check that there is no interaction between the covariate and each of the grouping variables. That is, it should be checked that the slope in the previous shown plot is the same for each group. The p-value obtained is `0.59` which indicates that this interaction is not statistically significant.

### Normality of Residuals

The Shapiro-Wilk Normality Test is run in the residuals of the model producing a p-value of `0.937` which is not significant and thus the normality of the residuals is assumed.

### Homogeneity of Variances

Levene's test is used to check that the variance of the residuals are equal between all the groups. The test yields a p-value of `0.482` which is again not significant and homogeneity of the residual variances is also assumed.
  

### ANCOVA Results  

ANOVA table:  

| Effect          | DFn  | DFd  | F      | p        | p < 0.05 |  
| --------------- | ---- | ---- | ------ | -------- | -------- |  
| round           | 1    | 53   | 2.408  | 0.127    | NO       |  
| weapon          | 2    | 53   | 21.982 | 1.12e-07 | YES      |  
| distance        | 1    | 53   | 10.568 | 0.002    | YES      |  
| weapon:distance | 2    | 53   | 0.202  | 0.818    | NO       |  

Findings:  
* No improvement over rounds.  
* No interaction between weapon and distance.  
* Strong main effects of both `weapon` and `distance`.  

An F test is run with the previous model and a reduced one, which has no interaction term and `round` is removed. The test yields a non significant p-value of `0.4306`, with statistic `F(53, 56) = 0.9345`, meaning that both models are statistically the same. 

---

### Pairwise Comparisons (Tukey HSD)  

Using the previous **reduced** model, a Tukey HSD (Honest Significant Difference) test is run, in order to quantify the differences between the groups. Here are the results of the test:

* The differences between the Phantom and the Sheriff are not significant. (p-value of `0.8645`).
* The Vandal produced an average of `5.30` more bots downed than the Sheriff. This value is significant (p-value = `0.0000004`). The 95% Confidence Interval for this parameter is (`3.1954, 7.4046`).
* The Vandal produced an average of `4.85` more bots downed than the Phantom. This value is significant (p-value = `0.0000024`). The 95% CI for this parameter is (`2.7454, 6.9546`).
* At long distance, the average of bots downed was `2.5667` **less** than at close distance. This value is significant (p-value = `0.0006838`). The 95% CI for this parameter is (`1.1368, 3.9965`).


## Conclusions  

1. **Vandal performed best**, significantly above Sheriff and Phantom.  
2. **Sheriff and Phantom were similar**, despite weapon differences.  
3. **Accuracy loss at long distance was consistent across weapons.**  
4. **No significant performance improvement across rounds.**  

## Disclaimer  

* This dataset is from a single player (me). Results may not generalize, but the methodology can be reproduced.  
* Shooting bots is not the same as competing against real players.  
* All code is on [GitHub](https://github.com/Sorkanius/articles/tree/master/valorant_aim).  
* Clips of me playing are on [YouTube](https://www.youtube.com/channel/UCYj6rQZTnRH0p6xi3qWJa_A).  

