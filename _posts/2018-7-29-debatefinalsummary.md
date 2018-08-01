---
layout: post
mathjax: true
title: Winning a Debate&#58; Insights from Intelligence Squared
---
 
How does one measure the winner of a debate? If the debate itself is the focus, it’s wholly unsatisfying to just tally up the opinions of the viewers, as that solely reflects the prior beliefs of the audience. Intelligence Squared is an excellent podcast which teaches policy through moderated debate. A resolution is proposed (e.g. “Affirmative Action does more harm than good”), and two teams of two experts argue the issue in a structured setting. Many other programs aim for balance with a halfhearted attempt to pay lip service to the opposing side (without truly believing it), so it’s refreshing to hear passionate and informed argument from both sides of the aisle. If one side cites a misleading statistic, their opponents are ready to actually call them out on it, penalizing the lazy rhetorical tactics that plague discourse in an echo chamber.

The focus of the program is the debate itself, but some nominal winner must still be declared. This is determined using a simple formula. The audience is polled before and after the debate (For, Against, or Undecided on the issue), and the side which gains the most votes is the winner. To be clear, I do not think that this method of declaring a winner is a particularly important to the show, nor do I think any listeners actually consider the swings of audience opinion to be the final say on the matter. But it’s still interesting to consider whether or not this is a reasonable way to declare a debate winner. And more broadly, what can the results of a debate tell us about the way that people make up their minds on polarizing issues? Much has been written about the politically charged climate of the present, and the ways in which the media we consume is entrenching us in our positions, leading to static and polarized political views. 

It turns out that Intelligence Squared doesn’t just measure the change in support for each side before and after the debate, for events since 2012 it also measured the individual shifts between each category (i.e. “Undecided before and For after”, or “For before and Against after”). This provides a small but insightful dataset looking at the ways in which minds can change on a polarizing issue in the course of an hour. 

### What does it mean to “win” a debate?

Intelligence Squared declares their nominal winner with a simple metric, measuring whether the For or Against side had the largest absolute percentage point shift in support from the pre debate to the post debate audience poll. Thus, if the For side originally had 40% support and the Against side 35% support before the debate, and after the debate the For side had 45% support and the Against side had 43% support, the 8 percentage point increase of the Against side trumps the 5 percentage point increase of the For side, and the Against side wins the night.

The problem with this metric stems from the fact  that *there are a variety of ways that opinions could shift between the three camps*. This is related to both its usage of absolute percentage point change (with no reference to the relative switch), as well as the presence of the undecided voters.

I stumbled upon [this](https://stats.stackexchange.com/a/94742) excellent post by whuber on StackExchange. I have the pleasure of knowing whuber in person, and in characteristic fashion he gives a thorough and insightful analysis of the ways in which we can have different shifts in support (among the different groups) for the same absolute result, in ways that make determining the winner quite difficult. We cite a simple example proposed by whuber in this post.

Consider a situation where originally 20% are For, 60% are Against, and 20% are undecided (we will write this as a vector $(.2, .6, .2)$. After the debate, the vector of support is $(.3, .4, .3)$. Under the ISM, this is a clear and decisive win for the For side, as they gained 10 percentage points of support, while the Against side lost 20 percentage points. However, there are a variety of between-group opinion switches that could lead to this result. whuber suggests that we write these between-group switches in a 3x3 transition matrix, where the $ij$th element represents the percent of the original supporters for the $i$th camp before the debate are supporters of the $j$th camp after the debate (with For as 1, Against as 2, and Undecided as 3). Then, this represents a plausible transition matrix for our final result.

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

> Here, 36% of the “Fors" changed to the other side while only 29% of the “Against" changed to the opposite opinion. Moreover, slightly more of the undecideds (36%) vs 32%) came out "against" rather than for. Although their numbers in this audience decreased, we have a situation (reminiscent of Simpson's Paradox) in which the “Against" faction clearly won the debate!

We have a possible outcome where Intelligence Squared would declare the “For” side a decisive winner, but in terms of percentage shifts, the “Against” side was more persuasive at *both* convincing Undecideds to join their side, and convincing those with a prior opinion to change their viewpoint. This clarifies the challenge inherent in determining the winner of a debate. When one side is initially quite unpopular, they have a larger population of possible voters that they can woo, which makes their proportional gains much higher in absolute terms. There’s certainly a plausible argument that absolute shifts in support should be the most important factor in determining a winner, but this does make the situation more complex. And it gets even messier in cases where  the sub-group switches don’t necessarily agree. We might see one side sway a much larger proportion of the Undecided voters, while being less successful at convincing those from the other side to switch. This could leave us without a conclusive winner.

### How has this worked in practice?

Looking at the results of the Intelligence Squared debates in reality, we see that the majority of results are uncontroversial. and there is little doubt which side was more convincing. In total, we gather the results from 88 debates from the Intelligence Squared [website](https://www.intelligencesquaredus.org/debates), with the process described [here](https://dylanpotteroconnell.github.io/debateresults2/), and the unpolished scraped dataset [here](https://github.com/dylanpotteroconnell/IntelSquaredProject/blob/master/votingresultsfinal.csv).

The first winning metric we consider is the Intelligence Squared Metric (ISM), which measures shift in absolute percentage of support. We next consider the Undisputed Metric (UDM), which uses the proportional switching outlined above, and only assigns a winner when one side *both* convinces a greater proportion of Undecided voters to join their side, as well as a greater proportion of the other side to switch their opinion. In cases when these do not agree, no winner is declared.

Thus the first question is whether there are cases where the UDM declares a winner which is the reverse of the ISM, like the toy example outlined above by whuber. It turns out that this is *not* an idle concern, and in fact there have been five debates with this result.

$$
\begin{table}[ht]
\centering
\begin{tabular}{rrrr}
  \hline
 & 1 & 2 & 3 \\ 
  \hline
1 & 0.50 & 0.13 & 0.42 \\ 
  2 & 0.42 & 0.84 & 0.42 \\ 
  3 & 0.08 & 0.02 & 0.16 \\ 
   \hline
\end{tabular}
\end{table}
$$


