# RF Ping Pong ELO

*This is a pilot of the system. Rules are subjected to changes at short notice.*

## TL;DR

First, calculate your expected score of the match:

$$\text{expected}\_\text{outcome} = \frac{1}{1 + 10^{(\text{opponent}\_\text{rating} - \text{your}\_\text{rating}) / 500}}$$

Then, we can update your score based on the outcome as
$$\text{new}\_\text{rating} = \lfloor\text{old}\_\text{rating} + 32 \times (\text{actual}\_\text{outcome} - \text{expected}\_\text{outcome})\rfloor,$$

where

$$
\text{actual\_score} = \begin{cases}
  1 & \text{if you win,} \\
  0 & \text{if you lose.}
\end{cases}
$$

Player starts with ELO ratings of 1000. The different ranks are

- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum: 1600-1999
- Diamond: 2000+

**After finishing your game**: Calculate your new ratings and put it on the ranking board, which should be seen easily in Random Forest.

GLHF! You can read the text below to understand the ranking calculation in detail.

## Basics

We use a numerical rating to represent the skill level of each player. The higher the rating, the better the player is assumed to be. When two players compete, their ratings are used to calculate the expected outcome of the game. The expected outcome is then compared to the actual outcome, and the ratings are adjusted accordingly.

**Expected Outcome**
Each player starts with an initial rating of 1000. Given a player and a opponent, to calculate new ratings after a game, we first need to calculate the expected outcome of the game:

$$\text{expected}\_\text{outcome} = \frac{1}{1 + 10^{(\text{opponent}\_\text{rating} - \text{player}\_\text{rating}) / 500}}$$

The expected outcome, ranges from 0 to 1, shows how likely the player wins against the opponent[^1]. The higher the player ratings, the higher the expected outcome.

**Updating Ratings**
After the game, we can update the player's rating based on the outcome. If the player wins the game, the actual outcome is 1, and if the player loses, the actual outcome is 0. The new rating is calculated as follows:

$$\text{new\_rating} = \lfloor\text{old\_rating} + 32 \times (\text{actual\_outcome} - \text{expected\_outcome})\rfloor,$$

where the floor function, denoted by $\lfloor x \rfloor$, rounds down to the nearest integer. The rating change depends on the difference between the expected outcome and the actual outcome, with larger differences resulting in larger rating changes.

**Rankings**
To provide a better sense of progression and achievement, we can associate different rating ranges with different rankings. The ranking system for our ping pong ELO rating system is

- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum: 1600-1999
- Diamond: 2000+

**Seasons**
To continually refresh the game, we have season

**Ranking Tracking**

1. Write your name on the board along with the starting rating (1000), if this is your first time.
2. After you finished the match, calculate your new ratings follow the formula and write it next to your name.
3. Write down your corresponding ranking.
4. Go for another game!

## Interpretations

Denote $R$, $E$ as the ratings and expected out come of the match. Given player $A$ and $B$ with ratings $R_A$ and $R_B$, respectively, the expected outcome w.r.t. player $A$ is

$$
E_A = \frac{1}{1 + 10^{(R_B - R_A)/500}}
$$

The value of $E_A \in (0,1)$ shows how likely the player A wins against the opponent B. For example, if the ratings of both the player and the opponent are 1000, the expected outcome is $0.5$. On the other hand, suppose player has a ratings of 1200, and the opponent has a ratings of 1000. The expected outcome is $\approx 0.72$. The interpretation is we assume that either the player and the opponent win 50% of the time in the previous case, while the player and the opponent are expected to win 72% and 28% of the time, respectively, in the latter case. In fact, we can see how the expected outcome changes, when we fix $\text{opponent\_rating} = 1000$ and vary $\text{player\_rating}$ in this figure:

<p align="center">
  <img src="/fig/elo.png" />
</p>

In addition, the expected outcome formulation is equivalent to

$$
E_A = \frac{Q_A}{Q_A + Q_B}
$$

where $Q_A = 10^{R_A/500}$ and $Q_B = 10^{R_B/500}$. It follows that for each 500 rating points A has over B, $E_A$ is magnified 10 times over $E_B$. For example,

$$
\begin{align*}
&R_B = 1000, R_A = 1500 \Rightarrow E_A = 10 \times E_B \approx 0.91 \\
&R_B = 1000, R_A = 2000 \Rightarrow E_A = 10^2 \times E_B \approx 0.99\\
\end{align*}
$$

[^1]: One can notice that this is the [logistic function](https://en.wikipedia.org/wiki/Logistic_function) with base 10, $L=1$, $k=1/500$, and $x_0 = \text{opponent\_rating}$.
