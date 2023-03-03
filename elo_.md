#  RF Ping Pong ELO
  
  
*This is a pilot of the system. Rules are subjected to changes at short notice.*
  
System for calculating ELO ratings for ping pong games at the Random Forest room.
  
##  TL;DR
  
  
First, calculate your expected score of the match:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{expected&#x5C;_outcome}%20=%20&#x5C;frac{1}{1%20+%2010^{(&#x5C;text{opponent&#x5C;_rating}%20-%20&#x5C;text{your&#x5C;_rating})%20&#x2F;%20400}}"/></p>  
  
  
Then, we can update your score based on the outcome as
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20&#x5C;&#x5C;=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%205%20&#x5C;times%20&#x5C;text{actual&#x5C;_outcome}%20+%2064%20&#x5C;times%20(&#x5C;text{actual&#x5C;_outcome}%20-%20&#x5C;text{expected&#x5C;_outcome})&#x5C;rfloor,"/></p>  
  
  
where
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{actual&#x5C;_score}%20=%20&#x5C;begin{cases}%20%201%20&amp;%20&#x5C;text{if%20you%20win,}%20&#x5C;&#x5C;%20%200%20&amp;%20&#x5C;text{if%20you%20lose.}&#x5C;end{cases}"/></p>  
  
  
For multiple games <img src="https://latex.codecogs.com/gif.latex?i"/> played in a row, <img src="https://latex.codecogs.com/gif.latex?&#x5C;text{actual&#x5C;_outcome}"/> is a binary vector, and
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%20&#x5C;sum_i%205&#x5C;times%20&#x5C;text{actual&#x5C;_outcome}_i%20&#x5C;&#x5C;+%2064%20&#x5C;times%20(&#x5C;sum_i&#x5C;text{actual&#x5C;_outcome}_i%20-%20&#x5C;sum_i&#x5C;text{expected&#x5C;_outcome}_i)&#x5C;rfloor,"/></p>  
  
  
Player starts with ELO ratings of 1000 (Bronze). The different ranks are
  
- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum I: 1600-1799
- Platinum II: 1800-1999
- Diamond: 2000+
  
**After finishing your game**, calculate your new ratings and put it on the ranking board, which should be seen easily in Random Forest.
  
GLHF! You can read the text below to understand the ranking calculation in detail.
  
##  Basics
  
  
We use a numerical rating to represent the skill level of each player. The higher the rating, the better the player is assumed to be. When two players compete, their ratings are used to calculate the expected outcome of the game. The expected outcome is then compared to the actual outcome, and the ratings are adjusted accordingly.
  
**Expected Outcome**
Each player starts with an initial rating of 1000. Given a player and a opponent, to calculate new ratings after a game, we first need to calculate the expected outcome of the game:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{expected&#x5C;_outcome}%20=%20&#x5C;frac{1}{1%20+%2010^{(&#x5C;text{opponent&#x5C;_rating}%20-%20&#x5C;text{player&#x5C;_rating})%20&#x2F;%20400}}"/></p>  
  
  
The expected outcome, ranges from 0 to 1, shows how likely the player wins against the opponent[^1]. The higher the player ratings, the higher the expected outcome.
  
**Updating Ratings**
After the game, we can update the player's rating based on the outcome. If the player wins the game, the actual outcome is 1, and if the player loses, the actual outcome is 0. The new rating is calculated as follows:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20&#x5C;&#x5C;=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%205%20&#x5C;times%20&#x5C;text{actual&#x5C;_outcome}%20%20+%2064%20&#x5C;times%20(&#x5C;text{actual&#x5C;_outcome}%20-%20&#x5C;text{expected&#x5C;_outcome})&#x5C;rfloor,"/></p>  
  
  
where the floor function, denoted by <img src="https://latex.codecogs.com/gif.latex?&#x5C;lfloor%20x%20&#x5C;rfloor"/>, rounds down to the nearest integer. The rating change depends on the difference between the expected outcome and the actual outcome, with larger differences resulting in larger rating changes.
  
**Multiple Games in a Row**
For a long chain of games <img src="https://latex.codecogs.com/gif.latex?i"/>, <img src="https://latex.codecogs.com/gif.latex?&#x5C;text{actual&#x5C;_outcome}"/> should be a binary vector. Thus, we can calculate the new rating at once by summing the chain of actual outcomes and expected outcomes (a vector if multiple players are present):
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%20&#x5C;sum_i%205&#x5C;times%20&#x5C;text{actual&#x5C;_outcome}_i%20&#x5C;&#x5C;+%2064%20&#x5C;times%20(&#x5C;sum_i&#x5C;text{actual&#x5C;_outcome}_i%20-%20&#x5C;sum_i&#x5C;text{expected&#x5C;_outcome}_i)&#x5C;rfloor,"/></p>  
  
  
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
  
**Example**
  
Suppose two players, Bro and Ilon with ratings <img src="https://latex.codecogs.com/gif.latex?1200"/> and <img src="https://latex.codecogs.com/gif.latex?1150"/>, respectively, play a series of 5 games on a normal day at Random Forest. The winner of the games in chronological order are Bro, Ilon, Bro, Bro, and Ilon. We see that the actual outcome vector w.r.t. Bro is <img src="https://latex.codecogs.com/gif.latex?O_{&#x5C;text{Bro}}%20=%20[1,0,1,1,0]"/> and Ilon is <img src="https://latex.codecogs.com/gif.latex?O_{&#x5C;text{Ilon}}%20=%20[0,1,0,0,1]"/>. To calculate the new ratings <img src="https://latex.codecogs.com/gif.latex?R&#x27;_{&#x5C;text{Bro}},%20R&#x27;_{&#x5C;text{Ilon}}"/> for Bro and Ilon, we first calculate their expected outcome as
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_{&#x5C;text{Bro}}%20=%20&#x5C;frac{1}{1%20+%2010^{(1150%20-%201200)&#x2F;400}}%20&#x5C;approx%200.5714,%20&#x5C;&#x5C;E_{&#x5C;text{Ilon}}%20=%20&#x5C;frac{1}{1%20+%2010^{(1200%20-%201150)&#x2F;400}}%20&#x5C;approx%200.4285."/></p>  
  
  
Given these quantities, we can finally calculate the new ratings, <img src="https://latex.codecogs.com/gif.latex?R&#x27;_{&#x5C;text{Bro}}"/> and <img src="https://latex.codecogs.com/gif.latex?R&#x27;_{&#x5C;text{Ilon}}"/>, using the expected outcomes and actual outcomes vectors for the two players as
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?R&#x27;_{&#x5C;text{Bro}}%20=%201200%20+%205%20&#x5C;times%20(1%20+%200%20+%201%20+%201%20+%200)%20&#x5C;&#x5C;+%2064&#x5C;times%20&#x5C;left((1%20+%200%20+%201%20+%201%20+%200)%20-%205&#x5C;times%200.5714%20&#x5C;right)%20=%201224,&#x5C;&#x5C;R&#x27;_{&#x5C;text{Bro}}%20=%201150%20+%205%20&#x5C;times%20(0%20+%201%20+%200%20+%200%20+%201)%20&#x5C;&#x5C;+%2064&#x5C;times%20&#x5C;left((0%20+%201%20+%200%20+%200%20+%201)%20-%205&#x5C;times%200.4285%20&#x5C;right)%20=%20%201150."/></p>  
  
  
##  Analysis
  
  
Denote <img src="https://latex.codecogs.com/gif.latex?R"/>, <img src="https://latex.codecogs.com/gif.latex?E"/> as the ratings and expected out come of the match. Given player <img src="https://latex.codecogs.com/gif.latex?U"/> and <img src="https://latex.codecogs.com/gif.latex?V"/> with ratings <img src="https://latex.codecogs.com/gif.latex?R_U"/> and <img src="https://latex.codecogs.com/gif.latex?R_V"/>, respectively, the expected outcome w.r.t. player <img src="https://latex.codecogs.com/gif.latex?U"/> is
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_U%20=%20&#x5C;frac{1}{1%20+%2010^{(R_V%20-%20R_U)&#x2F;400}}."/></p>  
  
  
The value of <img src="https://latex.codecogs.com/gif.latex?E_U%20&#x5C;in%20(0,1)"/> shows how likely the player U wins against the opponent V. For example, if the ratings of both the player and the opponent are 1000, the expected outcome is <img src="https://latex.codecogs.com/gif.latex?0.5"/>. On the other hand, suppose player has a ratings of 1200, and the opponent has a ratings of 1000. The expected outcome is <img src="https://latex.codecogs.com/gif.latex?&#x5C;approx%200.72"/>. The interpretation is we assume that either the player and the opponent win 50% of the time in the previous case, while the player and the opponent are expected to win 72% and 28% of the time, respectively, in the latter case. In fact, we can see how the expected outcome changes, when we fix <img src="https://latex.codecogs.com/gif.latex?&#x5C;R_V%20=%201000"/> and vary <img src="https://latex.codecogs.com/gif.latex?R_U"/> in this figure:
  
<p align="center">
  <img src="assets/elo.png" />
</p>
  
To apply this analysis to different opponent ratings, one can imagine shifting this curve to center on such ratings. In addition, the expected outcome formulation is equivalent to
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_U%20=%20&#x5C;frac{Q_U}{Q_U%20+%20Q_V}"/></p>  
  
  
where <img src="https://latex.codecogs.com/gif.latex?Q_U%20=%2010^{R_U&#x2F;400}"/> and <img src="https://latex.codecogs.com/gif.latex?Q_V%20=%2010^{R_V&#x2F;400}"/>. It follows that for each 400 rating points U has over V, <img src="https://latex.codecogs.com/gif.latex?E_U"/> is magnified 10 times over <img src="https://latex.codecogs.com/gif.latex?E_V"/>. For example,
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{align*}&amp;R_V%20=%201000,%20R_U%20=%201400%20&#x5C;Rightarrow%20E_U%20=%2010%20&#x5C;times%20E_V%20&#x5C;approx%200.91,%20&#x5C;&#x5C;&amp;R_V%20=%201000,%20R_U%20=%201800%20&#x5C;Rightarrow%20E_U%20=%2010^2%20&#x5C;times%20E_V%20&#x5C;approx%200.99.%20&#x5C;&#x5C;&#x5C;end{align*}"/></p>  
  
  
Denote the outcome <img src="https://latex.codecogs.com/gif.latex?O"/> w.r.t. player U as <img src="https://latex.codecogs.com/gif.latex?O_U"/>, and <img src="https://latex.codecogs.com/gif.latex?R&#x27;_A"/> as the new ratings of player <img src="https://latex.codecogs.com/gif.latex?U"/> after a match. The formula for updating player U's rating is
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?R&#x27;_U%20=%20R_U%20+%205O_U%20+%2064(O_U%20-%20E_U)"/></p>  
  
  
How do the rating gains for U differ for different <img src="https://latex.codecogs.com/gif.latex?R_U"/>? Assume U wins in a game against V, we conduct the same analysis with the expected outcome by fixing <img src="https://latex.codecogs.com/gif.latex?R_V=1000"/> and yield
  
<p align="center">
  <img src="assets/rating_gain.png" />
</p>
  
##  Redefining the Game
  
  
If the ELO system feels unbalanced, lopsided or unfair in general, we can modify the formulas for calculating the ratings.
  
**Expected Outcome**
Let us revisit the expected outcome formula and replace <img src="https://latex.codecogs.com/gif.latex?S=400"/>:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_U%20=%20&#x5C;frac{1}{1%20+%2010^{(R_V%20-%20R_U)&#x2F;S}}."/></p>  
  
  
We see that this is the generalized version of the expected outcome calculation. As mentioned, <img src="https://latex.codecogs.com/gif.latex?S"/> represents the ratings difference such that <img src="https://latex.codecogs.com/gif.latex?E_U"/> is magnified 10 times over <img src="https://latex.codecogs.com/gif.latex?E_V"/>. This corresponds to the assumption of the level of playing at different rating levels. In particular, we are assuming player <img src="https://latex.codecogs.com/gif.latex?U"/> with <img src="https://latex.codecogs.com/gif.latex?R_U"/> ratings have the skills to win 9 out of 10 matches against against player <img src="https://latex.codecogs.com/gif.latex?V"/> with <img src="https://latex.codecogs.com/gif.latex?R_U%20-%20S"/>. With <img src="https://latex.codecogs.com/gif.latex?S=500"/>, for example, we think that Gold-ranked players should win 9 out of 10 matches against Bronze players.
  
**Rating Updates**
Regarding the generalized rating updates formula, we encounter another tune-able parameter <img src="https://latex.codecogs.com/gif.latex?K"/> and <img src="https://latex.codecogs.com/gif.latex?B"/>:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?R&#x27;_U%20=%20R_U%20+%20B%20+%20K(O_U%20-%20E_U)"/></p>  
  
<img src="https://latex.codecogs.com/gif.latex?B"/> serves as a baseline of number of point given, such that this ELO system is not a zero-sum game. <img src="https://latex.codecogs.com/gif.latex?B"/> should be very small, around <img src="https://latex.codecogs.com/gif.latex?1&#x2F;20"/>th of the starting ELO, to prevent it from affecting the general competitive landscape. On the other hand, <img src="https://latex.codecogs.com/gif.latex?K"/> basically decides the sensitivity of the ratings system. When <img src="https://latex.codecogs.com/gif.latex?K"/> is high, winners gain more points and losers lose more, and vice versa. There are more advanced schemes for setting <img src="https://latex.codecogs.com/gif.latex?K"/>, mostly revolving around setting K higher when <img src="https://latex.codecogs.com/gif.latex?R_U"/> is higher. Nevertheless, we use a fixed <img src="https://latex.codecogs.com/gif.latex?K"/> for simplicity in our novel system.
  
[^1]: One can notice that this is the [logistic function](https://en.wikipedia.org/wiki/Logistic_function ) with base 10, <img src="https://latex.codecogs.com/gif.latex?L=1"/>, <img src="https://latex.codecogs.com/gif.latex?k=1&#x2F;400"/>, and <img src="https://latex.codecogs.com/gif.latex?x_0%20=%20&#x5C;text{opponent&#x5C;_rating}"/>.
  