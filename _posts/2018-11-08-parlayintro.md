---
layout: post
mathjax: true
title: A Gentle Introduction to Parlays and Independence
---

> Betting parlays of positively correlated events will generally be profitable. 

This is far from revolutionary, and is patently obvious to those familiar with betting. However, I've talked to a number of people with no prior experience in the area who are curious about understanding the basic idea behind this. And I wanted to practice writing a basic tuturial that only assumes some basic background in probability, and should still be somewhat informative for those without any prior experience. 

# Betting

Most people are familiar with the concept of a bet. I might flip a coin, and the loser pays the winner a dollar, which feels like a fair deal. Implicit in our evaluation of this bet as "fair" is the concept of Expected Value (EV). While this will avoid the mathematical definition whenever possible, we tend to have an intuitive sense of EV when we confront it in our daily lives. The fact that the coin flipping game is "fair" is obvious due to its symmetry. But if I instead offered to roll a die, and said that on a one through four you pay me a dollar, and on a five or six I pay you two dollars, many people would intuitively see this bet as fair as well. I have twice the chance to win, but have to pay out twice as much. Mathematically, we simply find the sum of all the outcomes multiplied by their probability of occurrence. I have $2/3$ chance to pay you a dollar, and $1/3$ chance to win two dollars, and $-1*\tfrac{2}{3} + 2*\tfrac{1}{3} = 0$.

When it comes to betting, it's worth noting that Expected Value is the *only* viable way to consider the outcome, and any other metric is bound to be a losing strategy in the long run. That doesn't mean it applies in every practical situation. If I offered you a deal where I would double your life's savings with a 55% chance, and you would lose it all with 45% chance, this is clearly a positive expected value bet, and it is also one that you should demonstrably *never* take. The marginal utility of money states that the value of doubling your life savings does not equate to the loss from losing your life's savings. This is a reminder that betting is particularly problematic in any situation where the stakes are not so low that you can focus solely on EV. If someone offered you the same deal on each of your individual dollars in your life savings independently, then it would be a consistently profitable proposition with essentially no downside, due to the law of large numbers. (And if one's life savings is not suitably large so that the law of large numbers applies, betting should be far from your mind).

# Numbers behind betting

In America, bets are usually denoted using a $\pm X-$hundred system. A $+200$ line says that if you bet $100 and win, you receive back \$200 plus your original bet.

roll two 6 sided dice. events: sum to at least 11, and first die is a 6.

in practice, concerns about *expectation* are the only relevant criteria when the stakes are small relative to ones means. People are absolutely right to be skeptical of this evaluation in other situations. If someone offered you a 55% chance to double or nothing your life's savings, this would be an unequivocally terrible deal for people in almost *any* life circumstance, despite its positive expectation. The first $1,000 dollars of your life's savings are worth vastly more than the last $1,000. However, when we are dealing with amounts that are relatively inconsequential compared to one's overall financial situation, lon term expectation can be focused on. One way to consider this is as a result of the law of large numbers. If one could run a given bet enough times that you are bound to see a large number of successes and failures (I hesitate to simply state a number of times because it varies depending on how imbalanced the success/failure probabilities are), then the law of large numbers ensures that you are vanishingly likely to not see a profit. In the example of the weighted coinflip for our life's savings, that is not a bet we could run many times. If we could instead run the same bet independently on every single individual dollar in our bank account, we would be nearly guaranteed a fantastical profit.
 
