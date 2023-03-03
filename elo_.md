#  RF Ping Pong ELO
  
*This is a pilot of the system. Rules are subjected to changes at short notice.*
  
##  TL;DR
  
  
First, calculate your expected score of the match:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{expected&#x5C;_outcome}%20=%20&#x5C;frac{1}{1%20+%2010^{(&#x5C;text{opponent&#x5C;_rating}%20-%20&#x5C;text{your&#x5C;_rating})%20&#x2F;%20500}}"/></p>  
  
  
Then, we can update your score based on the outcome as
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%2032%20&#x5C;times%20(&#x5C;text{actual&#x5C;_outcome}%20-%20&#x5C;text{expected&#x5C;_outcome})&#x5C;rfloor,"/></p>  
  
  
where
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{actual&#x5C;_score}%20=%20&#x5C;begin{cases}%20%201%20&amp;%20&#x5C;text{if%20you%20win,}%20&#x5C;&#x5C;%20%200%20&amp;%20&#x5C;text{if%20you%20lose.}&#x5C;end{cases}"/></p>  
  
  
Player starts with ELO ratings of 1000. The different ranks are
  
- Bronze: 1000-1199
- Silver: 1200-1399
- Gold: 1400-1599
- Platinum: 1600-1999
- Diamond: 2000+
  
**After finishing your game**: Calculate your new ratings and put it on the ranking board, which should be seen easily in Random Forest.
  
GLHF! You can read the text below to understand the ranking calculation in detail.
  
##  Basics
  
  
We use a numerical rating to represent the skill level of each player. The higher the rating, the better the player is assumed to be. When two players compete, their ratings are used to calculate the expected outcome of the game. The expected outcome is then compared to the actual outcome, and the ratings are adjusted accordingly.
  
**Expected Outcome**
Each player starts with an initial rating of 1000. Given a player and a opponent, to calculate new ratings after a game, we first need to calculate the expected outcome of the game:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{expected&#x5C;_outcome}%20=%20&#x5C;frac{1}{1%20+%2010^{(&#x5C;text{opponent&#x5C;_rating}%20-%20&#x5C;text{player&#x5C;_rating})%20&#x2F;%20500}}"/></p>  
  
  
The expected outcome, ranges from 0 to 1, shows how likely the player wins against the opponent[^1]. The higher the player ratings, the higher the expected outcome.
  
**Updating Ratings**
After the game, we can update the player's rating based on the outcome. If the player wins the game, the actual outcome is 1, and if the player loses, the actual outcome is 0. The new rating is calculated as follows:
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;text{new&#x5C;_rating}%20=%20&#x5C;lfloor&#x5C;text{old&#x5C;_rating}%20+%2032%20&#x5C;times%20(&#x5C;text{actual&#x5C;_outcome}%20-%20&#x5C;text{expected&#x5C;_outcome})&#x5C;rfloor,"/></p>  
  
  
where the floor function, denoted by <img src="https://latex.codecogs.com/gif.latex?&#x5C;lfloor%20x%20&#x5C;rfloor"/>, rounds down to the nearest integer. The rating change depends on the difference between the expected outcome and the actual outcome, with larger differences resulting in larger rating changes.
  
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
  
##  Interpretations
  
  
Denote <img src="https://latex.codecogs.com/gif.latex?R"/>, <img src="https://latex.codecogs.com/gif.latex?E"/> as the ratings and expected out come of the match. Given player <img src="https://latex.codecogs.com/gif.latex?A"/> and <img src="https://latex.codecogs.com/gif.latex?B"/> with ratings <img src="https://latex.codecogs.com/gif.latex?R_A"/> and <img src="https://latex.codecogs.com/gif.latex?R_B"/>, respectively, the expected outcome w.r.t. player <img src="https://latex.codecogs.com/gif.latex?A"/> is
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_A%20=%20&#x5C;frac{1}{1%20+%2010^{(R_B%20-%20R_A)&#x2F;500}}"/></p>  
  
  
The value of <img src="https://latex.codecogs.com/gif.latex?E_A%20&#x5C;in%20(0,1)"/> shows how likely the player A wins against the opponent B. For example, if the ratings of both the player and the opponent are 1000, the expected outcome is <img src="https://latex.codecogs.com/gif.latex?0.5"/>. On the other hand, suppose player has a ratings of 1200, and the opponent has a ratings of 1000. The expected outcome is <img src="https://latex.codecogs.com/gif.latex?&#x5C;approx%200.72"/>. The interpretation is we assume that either the player and the opponent win 50% of the time in the previous case, while the player and the opponent are expected to win 72% and 28% of the time, respectively, in the latter case. In fact, we can see how the expected outcome changes, when we fix <img src="https://latex.codecogs.com/gif.latex?&#x5C;text{opponent&#x5C;_rating}%20=%201000"/> and vary <img src="https://latex.codecogs.com/gif.latex?&#x5C;text{player&#x5C;_rating}"/> in this figure:
  
<p align="center">
  <img src="assets/elo.png" />
</p>
  
  
In addition, the expected outcome formulation is equivalent to 
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?E_A%20=%20&#x5C;frac{Q_A}{Q_A%20+%20Q_B}"/></p>  
  
  
where <img src="https://latex.codecogs.com/gif.latex?Q_A%20=%2010^{R_A&#x2F;500}"/> and <img src="https://latex.codecogs.com/gif.latex?Q_B%20=%2010^{R_B&#x2F;500}"/>. It follows that for each 500 rating points A has over B, <img src="https://latex.codecogs.com/gif.latex?E_A"/> is magnified 10 times over <img src="https://latex.codecogs.com/gif.latex?E_B"/>. For example,
  
<p align="center"><img src="https://latex.codecogs.com/gif.latex?&#x5C;begin{align*}&amp;R_B%20=%201000,%20R_A%20=%201500%20&#x5C;Rightarrow%20E_A%20=%2010%20&#x5C;times%20E_B%20&#x5C;approx%200.91%20&#x5C;&#x5C;&amp;R_B%20=%201000,%20R_A%20=%202000%20&#x5C;Rightarrow%20E_A%20=%2010^2%20&#x5C;times%20E_B%20&#x5C;approx%200.99&#x5C;&#x5C;&#x5C;end{align*}"/></p>  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
[^1]: One can notice that this is the [logistic function](https://en.wikipedia.org/wiki/Logistic_function ) with base 10, <img src="https://latex.codecogs.com/gif.latex?L=1"/>, <img src="https://latex.codecogs.com/gif.latex?k=1&#x2F;500"/>, and <img src="https://latex.codecogs.com/gif.latex?x_0%20=%20&#x5C;text{opponent&#x5C;_rating}"/>.
  