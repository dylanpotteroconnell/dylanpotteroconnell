---
layout: post
title: Results of Elo Model for Group Stage
---
Previously, I tried making a simple Elo rating system to model international football (i.e. soccer, referred to as just "football" here) national teams during the World Cup. Elo rating systems were used all around me, but I had never delved into the details of how they actually worked, so it was a useful exercise. It's impossible to draw precise inferences from a model until you actually understand what goes into it. The resulting Elo model should not be considered truly predictive. It operates with a huge restriction, utilizing only the data from past World Cups and the qualifying rounds (ignoring other international competitions). It also doesn't draw in any outside information such as injury news, or player retirements. Clearly, we could improve the predictive ability of our model by including more information. The advantage of this simple approach is that the resulting model is readily interpretable. It simply represents our best estimate of a team's strength based on their World Cup and qualifying results, and seeks to quantify those results in the most accurate way possible (because simply noting the wins and losses is woefully insufficient). Incorporating injury information is valuable, but there's no straightforward way to do it, and it would be a very subjective process. Thus, we could improve the specific predictions of our model, but we would lose the ability to properly interpret the results. Instead, I think it's more valuable to have a model that has a narrow but precise use. This provides a foundation which we can use as a baseline for our own subjective analysis (such as the loss of players to injury, or rumors about a team being poorly prepared, or not giving it their all during qualifying matches).

Even with this narrow approach, there are a vast number of decisions needed to implement an Elo rating system. While we try our best to leave subjective analysis outside of our model, many of these decisions do not have a clear right answer. In a past post (linked here), we outline some of these decisions. These range from home field advantage, the structure of temporal weighting of results (i.e. the "K factor"), and whether to include margin of victory rather than match result. We selected our final parameters using a variety of validation techniques, performing a search of the parameter space and comparing how predictive our model is along the way (compared to the observed results tempered with betting markets). This is just a brief summary, expanded upon in other posts, the point is that we result in an implementation of the Elo rating system that performs well for the peculiar situation of football national teams in the World Cup.

This preamble leads to the fact that the group stage just wrapped up, and it's interesting to take a look at the results. Now, simply comparing the actual match result to the percentage chance we predicted for that result isn't terribly insightful. Our model will always select the "favorite", so this would just tally up the total number of upsets. In fact, as a draw in football is essentially always less likely than the stronger team winning, this means that we would consider every draw to be an "incorrect" prediction. What we want to quantify is how shocking our model found those upsets relative to other norms.

The natural way to evaluate the Elo rating model is to consider the results of using it to blindly bet on the matches. Again, it cannot be said enough that there's little reason to think that the raw model would be an accurate tool to beat the betting markets.Bettors take into account all information available, and our model solely uses past results. However, it's a useful measure to see whether these predictions are at least plausible. Our final caveat here is that a null result doesn't mean all that much. If the betting markets were perfectly efficient, picking bets at random would be just as effective as any other strategy. Obviously, betting markets are *not* perfectly efficient, they represent our best collective wisdom, which may well be misguided. But it's important to remember that we would be surprised to find any betting strategy result in huge losses over a long period. The most telling "sanity check" of our model is that by and large, the odds it estimates are quite similar to the implied odds of the betting markets. This shows that the Elo rating system is at least a plausible model of team strength, and that it should be a valid starting point for subjective amendment (taking into account other factors beyond past team performance). 

| Team 1       | Team 2       | Model: P(Win 1) | Model: P(Draw) | Model: P(Win 2) | Line: Win 1 | Line: Tie | Line: Win 3 | Profit |
|--------------|--------------|-----------------|----------------|-----------------|-------------|-----------|-------------|--------|
| Russia       | Saudi Arabia | 0.562           | 0.233          | 0.206           | -217        | 334       | 807         | -100   |
| Russia       | Egypt        | 0.566           | 0.231          | 0.203           | -105        | 257       | 330         | 95.2   |
| Russia       | Uruguay      | 0.462           | 0.262          | 0.276           | 193         | 208       | 177         | -100   |
| Saudi Arabia | Egypt        | 0.36            | 0.289          | 0.351           | 445         | 263       | -130        | 445    |
| Saudi Arabia | Uruguay      | 0.278           | 0.262          | 0.46            | 1700        | 573       | -455        | -100   |
| Egypt        | Uruguay      | 0.274           | 0.261          | 0.465           | 744         | 309       | -196        | -100   |
| Portugal     | Spain        | 0.307           | 0.273          | 0.421           | 338         | 227       | 104         | -100   |
| Portugal     | Morocco      | 0.586           | 0.225          | 0.189           | -143        | 270       | 504         | -100   |
| Portugal     | Iran         | 0.528           | 0.243          | 0.23            | -167        | 309       | 539         | -100   |
| Spain        | Morocco      | 0.641           | 0.207          | 0.152           | -278        | 417       | 927         | -100   |
| Spain        | Iran         | 0.587           | 0.225          | 0.188           | -476        | 586       | 1832        | -100   |
| Morocco      | Iran         | 0.307           | 0.273          | 0.42            | 123         | 198       | 317         | 317    |
| France       | Australia    | 0.536           | 0.241          | 0.224           | -370        | 623       | 944         | -100   |
| France       | Peru         | 0.583           | 0.226          | 0.191           | -164        | 287       | 561         | -100   |
| France       | Denmark      | 0.447           | 0.266          | 0.287           | 106         | 181       | 434         | -100   |
| Australia    | Peru         | 0.407           | 0.276          | 0.316           | 193         | 254       | 149         | -100   |
| Australia    | Denmark      | 0.287           | 0.266          | 0.447           | 369         | 244       | -110        | -100   |
| Peru         | Denmark      | 0.25            | 0.251          | 0.498           | 271         | 211       | 131         | 131    |
| Argentina    | Iceland      | 0.635           | 0.209          | 0.156           | -303        | 441       | 1034        | -100   |
| Argentina    | Croatia      | 0.516           | 0.246          | 0.237           | 108         | 229       | 312         | -100   |
| Argentina    | Nigeria      | 0.583           | 0.226          | 0.191           | -200        | 426       | 499         | -100   |
| Iceland      | Croatia      | 0.257           | 0.254          | 0.488           | 341         | 299       | -120        | -100   |
| Iceland      | Nigeria      | 0.309           | 0.274          | 0.417           | 172         | 210       | 199         | 199    |
| Croatia      | Nigeria      | 0.428           | 0.271          | 0.301           | -147        | 279       | 514         | -100   |
| Brazil       | Switzerland  | 0.561           | 0.233          | 0.206           | -208        | 343       | 711         | -100   |
| Brazil       | Costa Rica   | 0.556           | 0.234          | 0.209           | -476        | 571       | 1817        | -100   |
| Brazil       | Serbia       | 0.571           | 0.23           | 0.199           | -213        | 368       | 661         | -100   |
| Switzerland  | Costa Rica   | 0.352           | 0.289          | 0.36            | -167        | 270       | 663         | -100   |
| Switzerland  | Serbia       | 0.367           | 0.287          | 0.346           | 196         | 204       | 175         | 196    |
| Costa Rica   | Serbia       | 0.371           | 0.286          | 0.343           | 436         | 243       | -119        | -100   |
| Germany      | Mexico       | 0.536           | 0.24           | 0.224           | -204        | 358       | 624         | 624    |
| Germany      | Sweden       | 0.56            | 0.233          | 0.207           | -213        | 366       | 664         | -100   |
| Germany      | South Korea  | 0.698           | 0.187          | 0.115           | -588        | 740       | 1837        | -100   |
| Mexico       | Sweden       | 0.381           | 0.283          | 0.335           | 121         | 240       | 259         | 259    |
| Mexico       | South Korea  | 0.547           | 0.237          | 0.216           | -143        | 281       | 473         | -100   |
| Sweden       | South Korea  | 0.523           | 0.244          | 0.233           | 127         | 204       | 293         | 127    |
| Belgium      | Panama       | 0.608           | 0.218          | 0.174           | -455        | 577       | 1662        | -100   |
| Belgium      | Tunisia      | 0.488           | 0.254          | 0.258           | -303        | 419       | 1104        | -100   |
| Belgium      | England      | 0.309           | 0.274          | 0.418           | 272         | 184       | 147         | 272    |
| Panama       | Tunisia      | 0.259           | 0.255          | 0.486           | 337         | 278       | -114        | -100   |
| Panama       | England      | 0.14            | 0.201          | 0.659           | 1773        | 548       | -455        | -100   |
| Tunisia      | England      | 0.216           | 0.237          | 0.547           | 755         | 323       | -204        | -100   |
| Poland       | Senegal      | 0.397           | 0.279          | 0.324           | 152         | 206       | 233         | 233    |
| Poland       | Colombia     | 0.204           | 0.232          | 0.563           | 249         | 246       | 121         | 121    |
| Poland       | Japan        | 0.314           | 0.276          | 0.411           | 171         | 215       | 195         | -100   |
| Senegal      | Colombia     | 0.179           | 0.221          | 0.6             | 419         | 282       | -132        | 75.8   |
| Senegal      | Japan        | 0.283           | 0.264          | 0.452           | 165         | 199       | 221         | -100   |
| Colombia     | Japan        | 0.512           | 0.248          | 0.24            | -112        | 234       | 411         | 411    |

