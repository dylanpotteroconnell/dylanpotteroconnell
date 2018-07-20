---
layout: post
title: NBA Power Rankings\: Predictive Efficacy
---


As outlined [here](https://dylanpotteroconnell.github.io/NBAPowerRankings1/), I thought it would be interesting to run a basic analysis of how effective sports power rankings are as a tool for predicting the results of games. Specifically, I will use the reddit.com/r/NBA biweekly community power ranking (for example, [this](https://www.reddit.com/r/nba/comments/5bpkd6/official_rnba_power_rankings_1_110716/) ranking), an aggregate poll that tends to fairly accurately reflect community consensus on what the power ranking should look like. I will focus on the 2015-2016 NBA season, as the power rankings over that season followed consistent timing and format.

We will examine three straightforward methods of predicting the results of a certain game.

1. Comparing the ranking of both teams in the most recent power ranking.
2. Comparing the win/loss record of the two teams, as of the most recent power ranking.
3. Comparing the average point differential (per game) of the two teams, as of the most recent power ranking.

A few issues are worth noting. As the goal is to examine the predictive ability of these power rankings, the point differential and win/loss records being compared are counted on the day of the most recent power ranking, not the day of the game itself (as it felt like that would be an unfair comparison). Next, while we always have a team that is considered "better" via our power rankings,  it is possible for the point differential and the win/loss record of these teams to be tied. In that case, we simply do not make a prediction. Finally, we entirely ignore the first two weeks of the season, as we do not have any previous win/loss record or point differential data to compare it to (the first power rankings come in the preseason). It seemed wrong to include these games, when we have nothing to compare the power ranking predictions to.

We iterate through each game in the 2015-2016 NBA regular season, and for each game, we compare the actual game result with each of our three predictions. Each “TRUE” implies that the prediction was correct, and "NA" means we did not make a prediction (for the reasons outlined above). The full code can be found [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/PowerRankingsScraper.R) for how to scrape the power rankings, [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/NBA15-16SeasonScraper.R) for how to scrape the list of games in the season, and [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/NBAResultPredicting.R) for calculating these results. Important note: I am extremely clueless with R, so this is likely done very poorly. Honestly the main goal of this project was not the results, but just learning how to scrape the data, so I can learn from this how to use R more effectively. The CSV of the results can be downloaded [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/prediction-16season.csv).



|                    | TRUE | FALSE | NA  |
|:------------------:|------|-------|-----|
| Power Ranking      | 742  | 383   | 105 |
| Win/Loss           | 722  | 373   | 135 |
| Point Differential | 735  | 389   | 106 |

|                    | Proportion of Correct Predictions |
|:------------------:|-----------------------------------|
| Power Ranking      | 0.6595556                         |
| Win/Loss           | 0.6593607                         |
| Point Differential | 0.6539146                         |



The results are not terribly shocking. Using the /r/NBA power ranking was a better predictive measure than simply using raw record or point differential, but there are some other interesting results to note.

First, none of these methods were able to correctly predict even just 2/3 of all NBA games. While theses approaches are extremely naive, and it is likely we could increase that proportion using other factors such as days of rest, day to day injuries, and other news, it is still interesting to see how frequent “upsets” are. To get a better sense of the proportion of upsets, one would need to find the Vegas odds for each game, but my bet is that this would only disagree with the power rankings predictions in very small proportion of games.

Next, it’s worth noting that the win/loss prediction proportion is deceptive because it did not pick a team for any game where the teams had equivalent records. To see this, note the number of NAs (where we made no prediction) for each method. For the power rankings, we only ignored the 105 games before the first in-season power rankings. For point differential, we also ignored one game where the teams had tied point differentials, but for win/loss record, we ignored 30 games where teams had equivalent records. These are generally the hardest games to predict, so a more honest proportion for this method might be to simply flip a coin for each game, and assume we expect to get half of these correct. Then, we would have to add 15 correct and 15 incorrect picks to our win/loss predicted results, so our new new proportion of correct predictions is then

~~~~
(722+15)/(373+722+30) = 0.6551111 
~~~~

|                    | Proportion of Correct Predictions |
|:------------------:|-----------------------------------|
| Power Ranking      | 0.6595556                         |
| Updated Win/Loss   | 0.6551111                         |
| Point Differential | 0.6539146                         |


Notably, this result is still barely superior to that of point differential, but the difference is extremely small (about 1.5 games different out of the 1230 game regular season). The difference that we wish to examine is between the Updated Win/Loss prediction and the Power Rankings prediction. Is there a meaningful difference in our ability to predict games using our power  ranking rather than just simple win loss?

The difference in these proportions is just 0.004444444, which equates to about 5.5 games different a year (out of the 1230 game regular season). Before we consider its statistical significance, it is worth noting that the effect size is quite small. However, there is no harm done in running a basic test of statistical significance, to see if this could be likely explained by random chance, so we can run a basic 2 sample proportion test.

~~~~
> prop.test(c(737,742),c(388+737, 742+383), alternative="less")

>data:  c(737, 742) out of c(388 + 737, 742 + 383)
X-squared = 0.03157, df = 1, p-value = 0.4295
alternative hypothesis: less
95 percent confidence interval:
 -1.00000000  0.02935915
sample estimates:
   prop 1    prop 2 
0.6551111 0.6595556 
~~~~

Thus, under these assumptions, there is no statistical significance to this difference in proportions. There is a ~40% chance that we would the Power Ranking prediction proportion be this much (or more) greater than the win/loss prediction proportion through random chance alone, if they actually had the same true proportion.

In summary, the /r/NBA power rankings does seem to provide a small improvement upon using simply win/loss record or point differential as a predictive tool. However, the difference is quite small (about ~5.5 games a year), small enough that it could eassily be the result of simply random chance. Next, I want to take a look at where these methods disagree. My assumption is that by the end of the season, the teams records generally agree with the power ranking, whereas they diverge more strongly near the start of the season.
