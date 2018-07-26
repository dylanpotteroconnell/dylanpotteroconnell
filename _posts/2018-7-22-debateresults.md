---
layout: post
mathjax: true
title: Winning a Debate&#58; Results from Intelligence Squared
---
 
How does one measure the winner of a debate? If the debate itself is the focus, it’s wholly unsatisfying to just tally up the opinions of the viewers, as surely that just reflects the opinion of the audience, which was likely largely decided before the debate had even begun. Intelligence Squared is an excellent podcast which teaches policy through moderated debate. A resolution is proposed (e.g. “Affirmative Action does more harm than good”), and two teams of two experts argue the issue in a structured setting. Many other programs aim for balance with a halfhearted attempt to pay lip service to the opposing side (without truly believing it), so it’s refreshing to hear passionate and informed argument from both sides of the aisle. If one side cites a misleading statistic, their opponents are ready to actually call them out on it, penalizing the lazy rhetorical tactics that plague discourse in an echo chamber.

The focus of the program is the debate itself, but some nominal winner must still be declared. This is determined using a simple formula. The audience is polled before and after the debate (For, Against, or Undecided on the issue), and the side which gains the most votes is the winner. To be clear, I do not think that this method of declaring a winner is a particularly important to the show, nor do I think any listeners actually consider the swings of audience opinion to be the final say on the matter. But it’s still interesting to consider, is this actually the best way to determine the winner of the debate? I’ll outline below why this isn’t quite as simple as it might seem. More broadly, what can the results of a debate tell us about the way that people make up their minds on polarizing issues? Much has been written about the politically charged climate of the present, and the ways in which the media we consume is entrenching us in our positions, leading to static and polarized political views. 

It turns out that Intelligence Squared doesn’t just measure the change in support for each side before and after the debate, for events since 2012 it also measured the individual shifts between each category (i.e. “Undecided before and For after”, or “For before and Against after”). This provides a small but insightful dataset looking at the ways in which minds can change on a polarizing issue in the course of an hour. 

### What does it mean to “win” a debate?

As mentioned above, it’s not always obvious how the unequivocally declare one side of the debate the winner. Intelligence Squared declares their nominal winner with a simple metric, measuring whether the For or Against side had the largest absolute percentage point shift in support from the pre debate to the post debate audience poll. Thus, if the For side originally had 40% support and the Against side 35% support before the debate, and after the debate the For side had 45% support and the Against side had 43% support, the 8 percentage point increase of the Against side trumps the 5 percentage point increase of the For side, and the Against side wins the night.

The problem with this metric stems from the fact  that *there are a variety of ways that opinions could shift between the three camps*. This is related to both its usage of absolute percentage point change (with no reference to the relative switch), as well as the presence of the undecided voters.

Imagine an overwhelmingly popular issue where 90% of the audience supports the For side, and 10% supports the Against, with no one undecided. After a heated debate, 85% of the audience supports the For side, and 15% support the Against side. The lack of undecided voters should make this result even more straightforward than most, and the Intelligence Squared Metric (ISM) would declare the Against side the winner, as it gained support (and the For side lost support). But even in this simple case, we note that there are a variety of ways for this result to occur. There could be 5% of people who began in the For camp, and switched to the Against camp, and no one else switched, which makes the Against camp look far more persuasive. However, what if 10% of people began in the For camp and switched to the Against camp, and 5% began in the Against camp and switched to the For side? We’d see the same absolute result. Yet, in this case, the For debaters convinced a full half of the original Against supporters  to change sides, while the Against debaters convinced only 1/9 of the original For side to change their minds. Can we really declare the Against side the winner? It depends on what you value in a debate. But personally the second scenario seems to persuasively argue that the absolute percentage point change used by the Intelligence Squared Metric (ISM) can mislead, and is essentially “rewarding” the Against side implicitly because they have a much larger possible audience that they can try and convert.

When I was doing a brief search to find whether a dataset for the Intelligence Squared debate results were compiled anywhere, I stumbled upon [this](https://stats.stackexchange.com/a/94742) excellent post by whuber on StackExchange. I have the pleasure of knowing whuber in person, and in characteristic fashion he gives a thorough and insightful analysis of the ways in which we can have different shifts in support (among the different groups) for the same absolute result. Specifically, whuber assumes that we have access to the percentage of support before and after the debate, and considers plausible ways to use these to estimate those between-group shifts. I encourage anyone interested to read his explanation of some of the mathematical structures that might make sense in this situation. In our case, I’ll simply cite his provided example, reproduced here, for how this can become particularly messy when undecided voters are included. He writes the result for a given debate using a 3x3 matrix, where the $ij$th element represents the percent of the original supporters for the $i$th camp before the debate are supporters of the $j$th camp after the debate (with For being 1, Against 2, and Undecided 3). 

Consider a situation where originally 20% are For, 60% are Against, and 20% are undecided (we will write this as a vector $(.2, .6, .2)$, and after the debate, we switch to $(.3, .4, .3)$. This would be a huge win for the For side, as it gained 10 percentage points of support, and the Against side lost 20 percentage points. However, one such transition matrix which could explain this result is suggested by whuber below.

$$
\mathbb{A} = \left(
\begin{array}{ccc}
 0.32 & 0.29 & 0.32 \\
 0.36 & 0.42 & 0.36 \\
 0.32 & 0.29 & 0.32 \\
\end{array}
\right).
$$

As whuber explains,

> Here, $36% of the “Fors" changed to the other side while only 29% of the “Against" changed to the opposite opinion. Moreover, slightly more of the undecideds (36%) vs 32%) came out "against" rather than for. Although their numbers in this audience decreased, we have a situation (reminiscent of Simpson's Paradox) in which the “Against" faction clearly won the debate!

This is a more elegant and powerful example of the situation outlined above, where a seemingly decisive win can arise from a side having worse relative performance in convincing both the Undecided and opposing camp.








