---
layout: post
mathjax: true
title: Winning a Debate&#58; Scraping the Intelligence Squared Dataset
---

In the last [post](https://dylanpotteroconnell.github.io/debateresults/), I gave an overview of some of the challenges in picking the winner of a debate, and how the Intelligence Squared podcast provides us with a dataset that we can use to examine both this and how opinion shifts in general.

### Compiling the Pages

The results from each Intelligence Squared debate are posted online in pages such as [this](https://www.intelligencesquaredus.org/debates/globalization-has-undermined-americas-working-class), including video of the debate, a description of the major positions of each side, the qualifications of the debaters, and most importantly, the results of the audience polling. Unfortunately, there doesn’t seem to be a central hub page that neatly lists all the URLs. However, the desired dataset isn’t huge (about 90 total debates), so there’s no substitute for the occasional work simply manually trawling through the website, and recording the date, name, and URL of each debate in question.

Luckily, the results themselves are generally presented in a consistent format.


```R
gsub(".*\"f\":\\{\"f\":(\\d+\\.*\\d*),\"a\":(\\d+\\.*\\d*),\"u\":(\\d+\\.*\\d*).*", "\\1 \\2 \\3", post)
```








https://github.com/dylanpotteroconnell/IntelSquaredProject/blob/master/intelsquareddata.R