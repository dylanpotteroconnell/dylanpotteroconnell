---
layout: post
mathjax: true
title: Winning a Debate&#58; Scraping the Intelligence Squared Dataset
---

In the last [post](https://dylanpotteroconnell.github.io/debateresults/), I gave an overview of some of the challenges in picking the winner of a debate, and how the Intelligence Squared podcast provides us with a dataset that we can use to examine both this and how opinion shifts in general.

### Compiling the Pages

The results from each Intelligence Squared debate are posted online in pages such as [this](https://www.intelligencesquaredus.org/debates/globalization-has-undermined-americas-working-class), including video of the debate, a description of the major positions of each side, the qualifications of the debaters, and most importantly, the results of the audience polling. Unfortunately, there doesn’t seem to be a central hub page that neatly lists all the URLs. However, the desired dataset isn’t huge (about 90 total debates), so there’s no substitute for the occasional work simply manually trawling through the website, and recording the date, name, and URL of each debate in question.

### Scraping the numbers

Once we have a full list of all the relevant URLs, luckily, the results themselves are generally presented in a consistent format. Thus,  some simple work with regular expressions gathers the data that we need. One such example.

```
debate_vote_results={"live":{"pre":{"f":36,"a":45,"u":19,"w":"a"},"post":{"f":32,"a":61,"u":7,"w":"a"},"s":41,"t":100,"f":{"f":18,"a":15,"u":3},"a":{"f":6,"a":38,"u":1},"u":{"f":8,"a":8,"u":3}},"online":{"t":100,"pre":{"f":50,"a":35,"u":15,"w":""},"post":{"f":44,"a":50,"u":6,"w":""},"f":{"f":35,"a":13,"u":2},"a":{"f":4,"a":27,"u":4},"u":{"f":6,"a":10,"u":0}}};
```

 In case anyone wants to borrow this sort of simple scrape for their own projects, you can find the code [here](https://github.com/dylanpotteroconnell/IntelSquaredProject/blob/master/intelsquareddata.R), although the approach is extremely messy. Luckily, with R it’s more important to be fast than it is to be clean, and you can use very awkward code as long as you find it readable and clear. Regular expressions like this can grab the relevant numbers that we need, and we store it in one large data frame.


```R
gsub(".*\"f\":\\{\"f\":(\\d+\\.*\\d*),\"a\":(\\d+\\.*\\d*),\"u\":(\\d+\\.*\\d*).*", "\\1 \\2 \\3", post)
```

where we identify the numbers that we’re interested in. With the numbers compiled into one large data frame (which can be viewed in raw form [here](https://github.com/dylanpotteroconnell/IntelSquaredProject/blob/master/votingresultsfinal.csv), for those interested in examining the data themselves). In the next post, we can actually dive into the data itself.






