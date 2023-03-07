# Baseball On-base Percentage Prediction
For this specific project, my goals were three-fold: (a) predict a players on-base percentage (OBP) for the 2021 season with the given data using a basic linear regression model, (b) suggest a better model that could be applied to handle data sets with missing data, and (d) suggest other metrics that could be added into the predictive model to better predict a players OBP.

There are multiple approaches we can take to understand baseball players OBP through different data sources both during training and in-game: Fielding metrics, batted ball data, player movement data and various laboratory or gym-based producers can be utilized to quantify fielding performance.

-	Batted Ball Data: we can take data from Statcast, FanGraphs or via various packages on Github (e.g., pitchRx, mlbgameday, baseball-analytics) to identify patterns or trends that Player X has struggled with in previous games. Primary data of interest could include exit velocity (EV), launch angle (LA), distance (D), and the type of hit that Player X may be struggling with (e.g., does Player X have a tough time predicting where fly balls will be going leading to a poor first step (FS)). Additionally, we can present the performance of league averages to identify areas where they may be underperforming when compared to other players.
-	Fielding Metrics: First step (FS), speed (S), throwing accuracy, and exchange time (ET; transfer the ball from the glove to throwing hand) are all key metrics that could elucidate if a fielder needs to become more conditioned, improve their footwork or better judge fly balls. Most of this data can be collected via Statcast or FanGraphs, but additional insights and context can be gained by looking at video footage for technical insights (such as Baseball Savant or Kintrax).
-	Laboratory or Gym-based Testing: various metrics (peak power, rate of force development, ground reaction forces) that have been correlated to bat swing velocity, home runs, total bases, stolen bases and fielding performance (Sources: 1, 2, 3, 4, 5) can be captured outside of the game. Such as performing a 1-repetition max back squat, a force-velocity profile (e.g., vertical jump testing with and without load, isometric mid-thigh pull, etc.) and upper body strength testing.

## Packages
```
suppressPackageStartupMessages({
  suppressWarnings({
    install.packages("tidyverse")
    install.packages("plotly")
    install.packages("rpart")
    install.packages("rpart.plot")
    install.packages("scales")
  })
})
```
  ## Example Code for Model
```
# Create a new column for total plate appearances
data$PA_total <- data$PA_21 + data$PA_20 + data$PA_19 + data$PA_18 + data$PA_17 + data$PA_16

# Fit a linear regression model to predict OBP_21 based on age, prior OBP, and total plate appearances
model <- lm(OBP_21 ~ age + OBP_20 + OBP_19 + OBP_18 + OBP_17 + OBP_16 + PA_total, data=data)

# Predict OBP_21 for each player
data$OBP_21_pred <- predict(model, newdata=data)
```
## LR Summary Stats
<p align="center">
<img width="600" height="350" src=images/lm_stats.png
</p>
  
## LR Plot Code
```{r plot}
# create the ggplot object
p <- ggplot(data, aes(x=OBP_21, y=OBP_21_pred, text=paste("Name: ", Name, "<br>",
                                                          "Predicted OBP: ", round(OBP_21_pred, 3), "<br>",
                                                          "Actual OBP: ", OBP_21, "<br>",
                                                          "Age: ", age))) +
  geom_point(aes(color = abs(OBP_21 - OBP_21_pred))) +
  scale_color_gradient(low = "green", high = "red") +
  geom_abline(intercept=0, slope=1) +
  labs(x="Actual On-Base Percentage", y="Predicted On-Base Percentage") +
  ggtitle("Actual vs. Predicted On-Base Percentage by Player") +
  theme_bw() +
  theme(text = element_text(size = 10))

# convert the ggplot object to a plotly object
ggplotly(p, tooltip = "text")
```
## LR Plot
<p align="center">
<img width="700" height="500" src=images/lm_figure.png
</p>
  
