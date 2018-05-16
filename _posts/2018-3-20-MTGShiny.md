---
layout: post
title: Shiny App for Analyzing MTG Design Trends
---

The challenge of data exploration frequently lies not in answering any specific question, but in the scope of possible questions which might be asked. Usual incentives promote us to analyze data in defense of a predefined narrative, but it can be just as valuable to construct methods for any user to conduct their own open ended exploration of the dataset. To do this requires interactivity with the user, which presents a substantially different challenge than what we usually face. I wanted to explore how to construct a simple Shiny app, which creates a slick and simple user interface for a given R program.

For an example open-ended data setting, I turned to one of my guilty pleasures, the trading card game "Magic: the Gathering". Magic (for short) has been in continuous development since 1993. A crucial aspect of the game's popularity is the near infinite customizability offered by the player's decision to choose exactly which cards are included in their deck before each game. Thus, both the business model and the game's lasting appeal is predicated on the regular release of new cards. In total, 19,989 unique cards have been created, although the frequent re-release of popular old cards means that the total number of cards chosen to be released in the last 25 years is around 35,000 (once including these repeats).

Thus, the total set of cards released creates a fascinating dataset. The philosphy behind Magic's design has continuously shifted over this period, as new designers join and leave the team and the tastes of their audience adjusts with the times. Some of these shifts in design are well known, but closer inspection might reveal trends that are not frequently acknowledged by the designers. Of course, this represents the exact srt of open-ended problem outlined above, where the individual questions asked might be simple, but the breadth of possible questions is staggering. This is a prime setup for user interaction, rather than directly presenting the user with the figures that we choose.

## Constructing the dataset

Before any work can be done in Shiny, we need a dataset to analyze. We thank the providers of the  [https://github.com/mtgjson/mtgjson](MTG JSON) project for their work in providing a complete dataset of all cards released. The cards are kept in JSON format, packed into sets (so that the set metadata can be stored), and listed within sets by cards. The JSON format makes sense over the usual R dataframe structure because of the broad array 
