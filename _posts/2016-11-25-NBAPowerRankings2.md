---
layout: post
title: NBA Power Rankings\: Scraping Data
---

In the [last post](https://dylanpotteroconnell.github.io/NBAPowerRankings1/), I provided a rambling introduction to sports power rankings. My goal is to provide a rudimentary analysis for the efficacy of NBA power rankings as a predictive tool (once the data is compiled, the actual analysis can be found [here](https://dylanpotteroconnell.github.io/NBARankingsPredictions/)). For this example, we will use the /r/NBA Reddit community power rankings, a poll involving one appointed fan representing each team. These provide a pretty well rounded representative rankings, as aggregate polls tend to be fairly representative, and less prone to shocking choices designed to generate discussion.  To compare the  accuracy of using power rankings to predict game results, versus using metrics like win/loss record or point differential. To do this, we need two pieces: a dataset of the NBA teams power rankings throughout the 2015-2016 season (we use the /r/NBA reddit community power rankings for this example), the teams' win/loss record and point differential on those dates, and then the actual observed results of the NBA season. We begin by compiling the power rankings data.

## Power rankings data

[Here](https://www.reddit.com/r/nba/comments/3qbhvw/official_rnba_power_rankings_1_102615/) is an example power ranking from last season. One could generate a full dataset by hand, as we have around 15 rankings per season, and 30 teams per ranking. However, the point of this is to get some experience with rudimentary data cleaning, so we will scrape from reddit using R. 

I have little experience scraping HTML webpages, but a cursory Google search shows that the basics are relatively straightforward. The resulting code is [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/PowerRankingsScraper.R). Now, I'll run through a summary of what I did here. This is likely a very clunky way of doing it, but there's some value in explaining one's thought process. 

For each team in each power rankings, we want the following information: their ranking, their record, and their point differential. Note that we are considering the 2015-2016 season only for this experiment as (unsurprisingly) the formatting of the reddit power rankings changes from year to year. The HTML for each team is stored as follows.

~~~~
<td align="right">16</td>
<td align="left"><a href="/BOS"></a> Celtics</td>
<td align="left">Despite a first round sweep to the the Championship runner-up Cavaliers, the Celtics made some strong pickups to help improve on their strong second half showing last season. After the acquisition of IT4 February, the C&#39;s followed it up by drafting Rozier at 16, Hunter at 28, and Mickey at 33. On July 9th, they signed veteran big man Amir Johnson and dumped Wallace’s expiring for former All Star David Lee. With the pickup of these veteran big men and the development of their young guys under Brad Stevens, the Celtics are primed for a strong season in the East.</td>
</tr>
~~~~

In this case, we can easily get the ranking of each team, but we need to compile the current standings (for wins/losses and point differential) for each team at this date. Luckily, Basketball Reference has a "historical standings" feature (for instance, [here](http://www.basketball-reference.com/friv/standings.cgi?month=11&day=9&year=2015&lg_id=NBA)). Basketball Reference is a fantastic resource for all things NBA, but it does not want web crawlers trawling through the site. Thus, we should be careful to be considerate and not make excessive calls to the site. Luckily, we need only generate the historical standings for each of the dates of the power rankings, so only about 14 times in total (very similar to the request load of an individual).

Thus, we write a function that takes in a date, and returns a data frame containing the win/loss record and point differential for every team on that date ("getHistoricalStandings"). An example of the relevant HTML in the BBR page for a given team is as follows.

~~~~
<tr class="full_table" >
<th scope="row" class="left " data-stat="team_name" csk="0.600" >
<a href="/teams/BOS/2016.html">Boston Celtics</a>*</th>
<td class="right " data-stat="wins" >2</td>
<td class="right " data-stat="losses" >3</td>
<td class="right " data-stat="win_loss_pct" >.400</td><td class="right " data-stat="gb" >3</td>
<td class="right " data-stat="wins_pyth" >3</td>
<td class="right " data-stat="losses_pyth" >2</td>
<td class="right " data-stat="pts_per_g" >103.6</td>
<td class="right " data-stat="opp_pts_per_g" >100.2</td>
</tr>
~~~~

One way of extracting the relevant data are basic gsub regex commands. For example, to get the wins and losses, we can run. If "z" denotes the variable of our scraped webpage (already truncated to just include the relevant sections, for convenience).

~~~~
wins <- as.numeric(gsub(".*data-stat=\\\"wins\\\" >([0-9]*)<.*", "\\1", z))
losses <- as.numeric(gsub(".*data-stat=\\\"losses\\\" >([0-9]*)<.*", "\\1", z))
~~~~

Thus, for a given power ranking, we compile a set of historical standings, then for each team, scrape their ranking, and add the W/L and PD to our data frame. One final annoyance to contend with is that the Reddit power rankings and BBR don't always agreee on the 3 digit codes with which they identify the teams. Thus, for convenience, we simply write two lists of the teamnames ("teamnames.reddit" and "teamnames.BBR"), and two functions to translate between the two. Thus, we call these functions to convert between the two sets of team codes.

The resulting CSV file for the 2015-2016 season can be found [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/powerrankings15-16.csv).


# Observed season results

The final piece of the puzzle is the actual results of the season, so that we can consider power rankings, win/loss records, and point differential as predictive tools. We need to compile a dataset of every game played in the 2015-2016 season, as well as the result of those games. The code for this is [here](https://github.com/dylanpotteroconnell/NBAPowerRankings/blob/master/NBA15-16SeasonScraper.R), and we briefly describe the process here. 

We again use Basketball Reference, which is nicely formatted (they have a list all games played in a given month [here](http://www.basketball-reference.com/leagues/NBA_2016_games-october.html)).
. Once again, BBR does not approve of automated crawling of its website, but in this case we will only make one call to the website per month, which is the same level of load as needed by a regular user. 

We follow the same process as above. For each month in the season, we grab the HTML of the page, and now we iterate through each game played in that month, extracting the teams that played, the resulting scores, and the date. An example of the HTML for one game is as follows.

~~~~
<tbody><tr ><th scope="row" class="left " data-stat="date_game" csk="201510270ATL" >
<a href="/boxscores/index.cgi?month=10&amp;day=27&amp;year=2015">Tue, Oct 27, 2015</a></th>
<td class=" " data-stat="game_start_time" >8:00 pm</td>
<td class="left " data-stat="visitor_team_name" csk="DET.201510270ATL" ><a href="/teams/DET/2016.html">Detroit Pistons</a></td>
<td class="right " data-stat="visitor_pts" >106</td>
<td class="left " data-stat="home_team_name" csk="ATL.201510270ATL" ><a href="/teams/ATL/2016.html">Atlanta Hawks</a></td>
<td class="right " data-stat="home_pts" >94</td>
<td class="center " data-stat="box_score_text" ><a href="/boxscores/201510270ATL.html">Box Score</a></td>
<td class="center " data-stat="overtimes" ></td>
<td class="left " data-stat="game_remarks" ></td></tr>
~~~~

Thus, an example gsub command to extract the date from this line of the HTML might be

~~~~
date <- gsub(".*month=([0-9]*)&amp;day=([0-9]*)&amp;year=([0-9]*)\\\">.*" , "\\1.\\2.\\3", z[i])
date <- as.POSIXct(date, format="%m.%d.%Y")
~~~~

One final annoyance to keep in mind is that this will also grab all playoff games. As we are only considering the regular season, we simply cut off the April games at the header titled "Playoffs" (as the playoffs only begin in April). The resulting CSV file can be found [here](). 

