# RF Ping Pong ELO

*This is a pilot of the system. Rules are subjected to changes at short notice.*

System for calculating ELO ratings for ping pong games at the Random Forest room.

## TL;DR

First, calculate your expected score of the match:

$$\text{expected\_outcome} = \frac{1}{1 + 10^{(\text{opponent\_rating} - \text{your\_rating}) / 400}}$$

Then, we can update your score based on the outcome as
$$\text{new\_rating} = \lfloor\text{old\_rating} + 5 \times \text{actual\_outcome} + 64 \times (\text{actual\_outcome} - \text{expected\_outcome})\rfloor,$$

where

$$
\text{actual\_score} = \begin{cases}
  1 & \text{if you win,} \\
  0 & \text{if you lose.}
\end{cases}
$$

For a long chain of games $i$, we can calculate the new rating at once by summing the chain of actual scores and expected scores:

$$\text{new\_rating} = \lfloor\text{old\_rating} + \sum_i 5\times \text{actual\_outcome}_i + 64 \times (\sum_i\text{actual\_outcome}_i - \sum_i\text{expected\_outcome}_i)\rfloor,$$

Player starts with ELO ratings of 1000. The different ranks are

- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum I: 1600-1799
- Platinum II: 1800-1999
- Diamond: 2000+

**After finishing your game**: Calculate your new ratings and put it on the ranking board, which should be seen easily in Random Forest.

GLHF! You can read the text below to understand the ranking calculation in detail.

## Basics

We use a numerical rating to represent the skill level of each player. The higher the rating, the better the player is assumed to be. When two players compete, their ratings are used to calculate the expected outcome of the game. The expected outcome is then compared to the actual outcome, and the ratings are adjusted accordingly.

**Expected Outcome**
Each player starts with an initial rating of 1000. Given a player and a opponent, to calculate new ratings after a game, we first need to calculate the expected outcome of the game:

$$\text{expected\_outcome} = \frac{1}{1 + 10^{(\text{opponent\_rating} - \text{player\_rating}) / 400}}$$

The expected outcome, ranges from 0 to 1, shows how likely the player wins against the opponent[^1]. The higher the player ratings, the higher the expected outcome.

**Updating Ratings**
After the game, we can update the player's rating based on the outcome. If the player wins the game, the actual outcome is 1, and if the player loses, the actual outcome is 0. The new rating is calculated as follows:

$$\text{new\_rating} = \lfloor\text{old\_rating} + 5 \times \text{actual\_outcome}  + 64 \times (\text{actual\_outcome} - \text{expected\_outcome})\rfloor,$$

where the floor function, denoted by $\lfloor x \rfloor$, rounds down to the nearest integer. The rating change depends on the difference between the expected outcome and the actual outcome, with larger differences resulting in larger rating changes.

**Rankings**
To provide a better sense of progression and achievement, we can associate different rating ranges with different rankings. The ranking system for our ping pong ELO rating system is

- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum I: 1600-1799
- Platinum II: 1800-1999
- Diamond: 2000+

**Ranking Tracking**

1. Write your name on the board along with the starting rating (1000), if this is your first time.
2. After you finished the match, calculate your new ratings follow the formula and write it next to your name.
3. Write down your corresponding ranking.
4. Go for another game (if you don't have anything else to do)!

## Analysis

Denote $R$, $E$ as the ratings and expected out come of the match. Given player $U$ and $V$ with ratings $R_U$ and $R_V$, respectively, the expected outcome w.r.t. player $U$ is
$$
E_U = \frac{1}{1 + 10^{(R_V - R_U)/400}}.
$$

The value of $E_U \in (0,1)$ shows how likely the player U wins against the opponent V. For example, if the ratings of both the player and the opponent are 1000, the expected outcome is $0.5$. On the other hand, suppose player has a ratings of 1200, and the opponent has a ratings of 1000. The expected outcome is $\approx 0.72$. The interpretation is we assume that either the player and the opponent win 50% of the time in the previous case, while the player and the opponent are expected to win 72% and 28% of the time, respectively, in the latter case. In fact, we can see how the expected outcome changes, when we fix $\R_V = 1000$ and vary $R_U$ in this figure:

<p align="center">
  <img src="/assets/elo.png" />
</p>

To apply this analysis to different opponent ratings, one can imagine shifting this curve to center on such ratings. In addition, the expected outcome formulation is equivalent to

$$
E_U = \frac{Q_U}{Q_U + Q_V}
$$

where $Q_U = 10^{R_U/400}$ and $Q_V = 10^{R_V/400}$. It follows that for each 400 rating points U has over V, $E_U$ is magnified 10 times over $E_V$. For example,

$$
\begin{align*}
&R_V = 1000, R_U = 1400 \Rightarrow E_U = 10 \times E_V \approx 0.91, \\
&R_V = 1000, R_U = 1800 \Rightarrow E_U = 10^2 \times E_V \approx 0.99. \\
\end{align*}
$$

Denote the outcome $O$ w.r.t. player U as $O_U$, and $R'_A$ as the new ratings of player $U$ after a match. The formula for updating player U's rating is

$$
R'_U = R_U + 5O_U + 64(O_U - E_U)
$$

How do the rating gains for U differ for different $R_U$? Assume U wins in a game against V, We conduct the same analysis with the expected outcome by fixing $R_V=1000$ and yield

<p align="center">
  <img src="/assets/rating_gain.png" />
</p>

## Redefining the Game

If the ELO system feels unbalanced, lopsided or unfair in general, we can modify the formulas for calculating the ratings.

**Expected Outcome**
Let us revisit the expected outcome formula and replace $S=400$:

$$
E_U = \frac{1}{1 + 10^{(R_V - R_U)/S}}.
$$

We see that this is the generalized version of the expected outcome calculation. As mentioned, $S$ represents the ratings difference such that $E_U$ is magnified 10 times over $E_V$. This corresponds to the assumption of the level of playing at different rating levels. In particular, we are assuming player $U$ with $R_U$ ratings have the skills to win 9 out of 10 matches against against player $V$ with $R_U - S$. With $S=500$, for example, we think that Gold-ranked players should win 9 out of 10 matches against Bronze players.

**Rating Updates**
Regarding the generalized rating updates formula, we encounter another tune-able parameter $K$ and $Q$:

$$
R'_U = R_U + B + K(O_U - E_U)
$$

$K$ basically decides the sensitivity of the ratings system. When $K$ is high, winners gain more points and losers lose more, and vice versa. On the other hand, $B$ serves as a baseline of number of point given, so this ELO system is not a zero-sum game. $B$ should be very small, around $1/20$th of the starting ELO, to prevent it from affecting the general competitive landscape.

[^1]: One can notice that this is the [logistic function](https://en.wikipedia.org/wiki/Logistic_function) with base 10, $L=1$, $k=1/400$, and $x_0 = \text{opponent\_rating}$.
