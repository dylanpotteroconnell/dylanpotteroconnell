---
layout: post
mathjax: true
title: Shiny App for Analyzing MTG Design Trends
---

The challenge of data exploration frequently lies not in answering any specific question, but in the scope of possible questions which might be asked. Usual incentives promote us to analyze data in defense of a predefined narrative, but it can be just as valuable to construct methods for any user to conduct their own open ended exploration of the dataset. To do this requires interactivity with the user, which presents a substantially different challenge than what we usually face. I wanted to explore how to construct a simple Shiny app, which creates a slick and simple user interface for a given R program.

For an example open-ended data setting, I turned to one of my guilty pleasures, the trading card game "Magic: the Gathering". Magic (for short) has been in continuous development since 1993. A crucial aspect of the game's popularity is the near infinite customizability offered by the player's decision to choose exactly which cards are included in their deck before each game. Thus, both the business model and the game's lasting appeal is predicated on the regular release of new cards. In total, 19,989 unique cards have been created, although the frequent re-release of popular old cards means that the total number of cards chosen to be released in the last 25 years is around 35,000 (once including these repeats).

Thus, the total set of cards released creates a fascinating dataset. The philosophy behind Magic's design has continuously shifted over this period, as new designers join and leave the team and the tastes of their audience adjusts with the times. Some of these shifts in design are well known, but closer inspection might reveal trends that are not frequently acknowledged by the designers. Of course, this represents the exact sort of open-ended problem outlined above, where the individual questions asked might be simple, but the breadth of possible questions is staggering. This is a prime setup for user interaction, rather than directly presenting the user with the figures that we choose.

## Constructing the dataset

Before any work can be done in Shiny, we need a dataset to analyze. We thank the providers of the  [MTG-JSON](https://github.com/mtgjson/mtgjson) project for their work in providing a complete dataset of all cards released. The cards are kept in JSON format, packed into sets (so that the set metadata can be stored), and listed within sets by cards. Each card in Magic follows a similar structure. It falls into one of several card types, has an array of attributes (from functional changes to its effect, to its collector number and name of the artist who drew its art), as well as a textbox which lists its specific effect. However, the JSON format makes sense over the rigid data frame structure because of the inconsistent formatting of cards throughout history. Magic stays fresh by following the mantra that "rules are made to be broken". Thus, while each card has a corresponding "color", after the first few sets, cards could have multiple colors. And as the game progressed, new card types were added, each with their own set of unique attribute data (beyond what would go in the text box of the effect). And eventually we saw the introduction of "split cards" and "double faced cards", which require their own unique structure. Ultimately, this provides a substantial challenge to coerce into a regular data frame. While every card could be summarized by unstructured text, our analysis will hinge on being able to rely on consistent card structure. We only consider a subset of cards which follow consistent structure, focusing solely on "creatures", which form the cornerstone of Magic (ignoring cards with irregular structure like flip cards). With this structure, it is straightforward to coerce the data to a data frame. We then use binary tags to mark whether a creature is each of the five possible colors, as well as whether it has one of about 40 different keywords of note. These keywords are simply noted in the textbox of the cards, so we must use careful regular expressions to ensure we only flag cards with the right keyword (for example, many cards use “flying” in their textbox to refer to a name, rather than the mechanic).

## Shiny App

$$a^2 + b^2 = c^2$$

abc 123 testing mathjax
newtest
