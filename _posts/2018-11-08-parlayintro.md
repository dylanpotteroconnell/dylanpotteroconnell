---
layout: post
mathjax: true
title: A Gentle Introduction to Parlays and Independence
---

> Betting parlays of positively correlated events will generally be profitable. 

This is far from revolutionary, and is patently obvious to those familiar with betting. However, I've talked to a number of people with no prior experience in the area who are curious about understanding the basic idea behind this. And I wanted to practice writing a basic tuturial that only assumes some basic background in probability, and should still be somewhat informative for those without any prior experience. 

# Betting

Most people are familiar with the concept of a bet. I might flip a coin, and the loser pays the winner a dollar, which feels like a fair deal. Implicit in our evaluation of this bet as "fair" is the concept of Expected Value (EV). While this will avoid the mathematical definition whenever possible, we tend to have an intuitive sense of EV when we confront it in our daily lives. The fact that the coin flipping game is "fair" is obvious due to its symmetry. But if I instead offered to roll a die, and said that on a one through four you pay me a dollar, and on a five or six I pay you two dollars, many people would intuitively see this bet as fair as well. I have twice the chance to win, but have to pay out twice as much. Mathematically, we simply find the sum of all the outcomes multiplied by their probability of occurrence. I have $2/3$ chance to pay you a dollar, and $1/3$ chance to win two dollars, and $-1*\frac{2}{3} + 2*\frac{1}{3} = 0$.

When it comes to betting, it's worth noting that Expected Value is the *only* viable way to consider the outcome, and any other metric is bound to be a losing strategy in the long run. That doesn't mean it applies in every practical situation. If I offered you a deal where I would double your life's savings with a 55% chance, and you would lose it all with 45% chance, this is clearly a positive expected value bet, and it is also one that you should demonstrably *never* take. The marginal utility of money states that the value of doubling your life savings does not equate to the loss from losing your life's savings. This is a reminder that betting is particularly problematic in any situation where the stakes are not so low that you can focus solely on EV. If someone offered you the same deal on each of your individual dollars in your life savings independently, then it would be a consistently profitable proposition with essentially no downside, due to the law of large numbers. (And if one's life savings is not suitably large so that the law of large numbers applies, betting should be far from your mind).

# Numbers behind betting

In America, bets are usually denoted using the Moneyline system, where odds are reported in the form of $\pm X-$[hundred]. A $+200$ line says that if you bet \\$100 and win, you receive back \\$200 plus your original bet. A $-250$ line says that if you bet \\$250 and win, you win \\$100 (and of course also win back your original bet). This system makes it intuitive to measure your payout on a specific bet, but it is somewhat opaque when newer bettors try and understand the corresponding probabilities associated with each bet. We can work in terms of "Implied Odds", which are simply the corresponding percentage chance that you would need to win for the bet to be breakeven in expected value. A +200 Moneyline has corresponding Implied Odds of 33.3%. Abroad, odds are frequently reported in fractional terms, a +200 Moneyline corresponds to 2/1 ("two to one") odds. We note that Moneyline odds will be in the form of plus or minus at least $100$. American sports will also report bettling lines such as the New England Patriots being $-7.5$ for their game. This is a *point spread*, where there is even money to be made betting whether the Patriots will score at least $7.5$ more points than their opponent. I personally find it nearly impossible to quickly understand Moneyline odds in terms of probabilities, so I built a light [Chrome extension](https://chrome.google.com/webstore/detail/odds-converter/klechkhopfnjihobbcfeheooaigjjgdg) that can quickly perform these conversions on the fly.


# Parlays



roll two 6 sided dice. events: sum to at least 11, and first die is a 6.

 
