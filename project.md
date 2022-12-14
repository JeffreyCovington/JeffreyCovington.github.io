
## Introduction
Chess is a two-player board game with a rich history and thriving competetive culture.
Competitive games of chess are played with a chess clock, a special clock which keeps track of the time allotted to each player to complete the game.

It is well known that having a low amount of time relative to one's opponent, also known as "time trouble", is detrimental to one's performance.
In fact, giving oneself less time at the start of the game is often used as a handicap against weaker players, such as in simultaneous exhibitions.

Time management during a game is considered a key skill of a chess player. It would be very useful for players to be aware of the causal impact that time trouble has on the outcome of the game, especially relative to other factors like overall player skill.

This project considers two main questions:
- What is the causal impact on the game outcome of having a time handicap at the start of the game?
- What is the causal impact of having less time than one's opponent in any given position in the middle of a game?

## Definition of Variables

Chess is a two-player game. Each player controls the pieces of a single color, either white or black. The object of the game is to put the opponent's king in checkmate, in which case that player wins. There are also circumstances where the game is declared a draw.

- There are three possible outcomes to a chess game: a win, a draw, and a loss. These are often encoded by the point values given in a tournament for each result: 1, 0.5, and 0 respectively. In this project these scores will always be given from the perspective of White.
- Time pressure has an impact on each players performance in a game. Having a low amount of time relative to one's opponent negatively impacts one's performance. The goal of this project is to quantify this impact in the presence of confounding factors.
- Player skill is often measured by an Elo-based rating system. A rating is a number assigned to a player where higher numbers relate to a higher skill level. The rating system predicts the outcome of the game based on the difference in the players' ratings. For example, if player A has a rating of 1800 and player B has a rating of 1400. For a 400 point difference, the rating system predicts player A will score 10 times as many points as player B for an expected score of approximately 90.9%.
- The quality of a chess position, i.e. who has the advantage in a given position, influences the outcome of the game. The starting position of chess is symmetric and White moves first. This is known to be a slight advantage and in practice White scores 53% from the starting position. However, due to the exponential growth of possible positions after a few moves, statistical estimates like this are not feasible for most chess positions. As an alternative, chess playing computer programs can be used to evaluate a position, but this has its own issues.
- A player's "desire to win" also has an impact on the outcome. Because a draw might be a more undesireable outcome in a tournament situation, players may take more risks. In addition players may play more quickly depending on the type of tournament.
- The time control itself impacts the outcome. It is well known that the shorter the time control, the less frequent draws are. In addition the presence or absence of increment, i.e. extra time allocated for each individual move, can effect how comfortable players are playing with a low allocation of time.


## Data

[lichess.org](lichess.org) is popular website for playing chess. The [lichess.org open database](https://database.lichess.org/) contains over 3.7 billion standard games of chess played on the site since 2010. The database contains information about the moves played in each game as well as the time remaining for each side after each move. The games are played at a variety of time controls, i.e. time allocations for each player. Common time controls can range from less than a minute for each player to complete the game to hours.

Uniquely, lichess.org offers [tournaments](https://lichess.org/tournament/help?system=arena) where players can opt to cut their time in half before the game in order to score more points for winning. This is known as "berserking" and will serve as useful data for answering the causal questions.

## Explanation of variables:
- $\mathrm{Outcome}$ is the outcome of the game from White's perspective. It takes the values 0, 1, 2 for a loss, draw, or win for White respectively.
- ${\mathrm{Rating}}$ is the Glicko 2 rating for White (W) and Black (B). A rating is typically between 0 and 4000 and rounded to the nearest integer.
- ${\mathrm{Streak}}$ encodes whether White (W) and Black (B) are on a streak, affecting the relative value of a win in the tournament standings. After winning two games in a row, a player earns double points for a win. A streak is broken after a draw or loss. This is encoded as 0, 1, 2 for no streak, 1 win streak, and 2+ win streak respectively.
- $\mathrm{NoDraw}$ represents whether White (W) and Black (B) are averse to making a draw. This is an unobserved variable as it represents the player's state of mind. While the outcome of a normal chess game is zero-sum, in the context of a tournament players may view a draw as worth less. 
- $\mathrm{Berserk}$ represents whether White (W) and Black (B) choose to berserk, i.e. cut their starting time in half for the opportunity of scoring an extra point. This is the variable that will be intervenned on.
- $\mathrm{TC}$ is the time control, i.e. the initial allotment of time for each player.

## Proof of Identification

![The DAG for this setup.](/dag.png)

The set $X_A = \\{\mathrm{Rating}_W, \mathrm{Rating}_B, \mathrm{Streak}_W, \mathrm{Streak}_B, \mathrm{TC}\\}$ $d$-separates $\mathrm{Berkserk}_W$ and $\mathrm{Berkserk}_B$ from $\mathrm{Outcome}$. Further, because $\mathrm{Outcome}$ is a collider $X_A$ is $d$-deparated from $b_W$ and $b_B$. Therefore, using backdoor adjustment $\mathrm{Outcome}(b_W, b_B)$ is identified by

$E[\mathrm{Outcome}(b_W, b_B)] = E[E[\mathrm{Outcome} \mid \mathrm{Berserk}_W = b_W, \mathrm{Berserk}_B = b_B, \mathrm{Rating}, \mathrm{Streak}, \mathrm{TC}]]$

A major assumption that is made is that each player's decision to berserk is idependent from eachother. This may not be the case, as players have the opportunity to berkserk after seeing their opponents decision. This could be modeled by a sequential decision making process, but this adds a huge amount of complexity, so this simplifying assumption is made.

## Identification

The outcome of a chess game within an arena-format tournament depends, amoung many factors, on a player's choice to "berserk", i.e. reduce their initial time by half for the opportunity to score more tournament points.

From the [proof of identification](./id) the outcome was identified as

$E[\mathrm{Outcome}(b_W)] = E[E[\mathrm{Outcome} \mid \mathrm{Berserk}_W = b_W, \mathrm{Berserk}_B, \mathrm{Rating}, \mathrm{Streak}]]$

and

$E[\mathrm{Outcome}(b_B)] = E[E[\mathrm{Outcome} \mid \mathrm{Berserk}_B = b_B, \mathrm{Berserk}_W, \mathrm{Rating}, \mathrm{Streak}]]$

for each player, White and Black, respectively.

Here $\mathrm{Outcome}$ is the outcome of the game from White's perspective, taking values of 0, 0.5, and 1 for a loss, draw, and win.
${\mathrm{Rating}}$ is the Glicko-2 rating for White (W) and Black (B).
Glicko is an Elo-based rating system where player's ratings are based on the logit of their expected outcome.
${\mathrm{Streak}}$ encodes whether White (W) and Black (B) are on a streak, affecting the relative value of a win in the tournament standings. After winning two games in a row, a player earns double points for a win. A streak is broken after a draw or loss. This is encoded as 0, 1, 2 for no streak, 1 win streak, and 2+ win streak respectively.
$\mathrm{Berserk}$ represents whether White (W) and Black (B) choose to berserk, i.e. cut their starting time in half for the opportunity of scoring an extra point.

## Estimation

Logistic outcome regression is used to estimate each of $E[\mathrm{Outcome}(b_W)]$ and $E[\mathrm{Outcome}(b_B)]$ for each case of $b_W = 0, 1$ and $b_B = 0, 1$. Without loss of generality we detail the algorithm for

$E[\mathrm{Outcome}(b_W)] = E[E[\mathrm{Outcome} \mid X, \mathrm{Berserk}_W = b_W]]$

where

$X = (1, \mathrm{Berserk}_B, \mathrm{Rating}, \mathrm{Streak})$

To enforce that the expected outcome lies in $[0, 1]$, we use logistic regression where we model

$E[E[\mathrm{Outcome} \mid X, \mathrm{B}_W] = \operatorname{logit}(\mu(X, \mathrm{B}_W; \theta)) := \eta(X, \mathrm{B}_W; \theta)$

where $\theta$ is a set of parameters to be fit.
Using $\eta := \theta^T (X, \mathrm{B}_W)^{T}$ subject to the constraint that $E_N[(\mathrm{Outcome} - \mu(X, \mathrm{B}_W; \theta) (X, \mathrm{B}_W)^{T}] = 0$ which can be solved using Newton's method.
Finally taking the expectation of the model gives the desired causal estimand.

Chess players often interpret expected scores in terms of an equivalent rating difference. For a given rating difference $R$, the expected score, $E$, is given by

$E = \frac{1}{1 + 10^{-R/400}}$

Then for an expected score $E$, the equivalent rating difference is given by

$R = 400 \log_{10}\left(\frac{E}{1-E}\right)$

Then for this analysis the results will also be reported as the equivalent difference in rating between choosing to berserk and not.

## Results

Data was collected from the [January 2022 lichess titled arena](https://lichess.org/tournament/jan22lta), a tournament with cash prizes that was only open to professional players.
Overall 7000 games of chess were played.

In the case where the causal effect of White berserking is tested, the results are as follows:

| Estimand | Score | Equivalent Rating |
| --- | --- | --- |
| E[\mathrm{Outcome}(b_W = 0)] | 54.07% | +28.4 |
| E[\mathrm{Outcome}(b_W = 1)] | 32.54% | -126.7 |

From the results, White choosing to berserk, i.e. reducing their alloted time by half, shows a clear negative effect in the results, equivalent of a -155 point decrease in performance.
To chess players, this is a large amount. In the United States Chess Federation, for example, player categories are divided into 200 point ranges affecting tournament eligability and titles.

Overall, outside of this analysis, White tends to scores 52% at this time control for an equivalent rating increase of 13.9.
In the context of this analysis, White scores higher at 54% without berserking, which may be attributable to games in which White does not berserk and Berserks.

In the case where Black chooses to berserk, the results are as follows. Note that the score and rating are given from White's perspective.

| Estimand | Score | Equivalent Rating |
| --- | --- | --- |
| E[\mathrm{Outcome}(b_B = 0)] | 50.99% | +6.9 |
| E[\mathrm{Outcome}(b_B = 1)] | 70.90% | +154.7 |

By choosing to berserk, the results suggest that Black goes from scoring around 49% (from Black's perspective) to about 29% for an equivalent rating decrease of -148 points.
This is roughly in line with the results for White. In this analysis, by choosing not to berserk, Black scores slightly better (49%) compared to Black's usual expected score of 48%.
As with the analysis for White, this may be attributable to playing opponents who chose to berserk.

## Interpretation of Results

Chess players have a lot of experience interpreting differencing in chess ratings.
This analysis suggests that choosing to berserk reduces one's performance by approximately 150 rating points, which should be taken into account when making this choice.
The ultimate goal of this analysis is not just to estimate the effect of berserking, but to estimate the effect of time trouble in general.
It should be noted that these results differ from estimates given by Kenneth Regan, who suggests that reducing one's time by half at this time control results in approximately a 267 point drop in performance.

## Discussion of Assumptions and Sensitivity Analysis

It is well known that time trouble has a significant effect on the outcome of a chess game. Having less time to make decisions leads to poor decision making. This is backed up by the collective experience of the chess community. Then the question needed to answer is what the magnitude of the effect is.

To investigate this we can look at data from lichess.org, namely the results of their the arena-format tournaments. Because players can choose to "berkserk", i.e. reduce their initial time by half for the chance to score more points, this can provide valuable data on the effect of time trouble. By controlling for the Elo rating of the players and their tournament standings, we found that, on average, choosing to berserk reduced one's performance by approximately 150 rating points, for an equivalent expected score of 29% against an equal opponent.

However, the decision to berserk or not comes down to the individual psychology of the players, which is rife with potential unmeasured confounding factors. In this section we will examine the psychological and modelling assumptions used in this study.

### Player Psychology and Unmeasured Confounding

Our data only included tournaments from the lichess.org Titled Arenas: tournaments with substantial cash prizes that are only open to professional players. Because of this we made the assumption that players are acting in their own self interest when making the decision to berserk or not based on their experience and the information available to them.

However, players have two distinct interests in the context of an arena tournament: doing well in an individual game and doing well in the tournament. For most games these interests are aligned, but chess allows for draws. A draw is a neutral result for the game but can be a negative result for the tournament standings by breaking a players winning streak (which awards extra points for consecutive wins.)

As such we focused on two confounding factors that affect the outcome of the game: the relative skill of each player and each players' desire to avoid a draw. Both of these factors are reflected the information available to each player: the relative Elo rating of the players and the winning streak of each player. Under the causal assumptions used in the proof of identification, we showed that controlling for these measured variables were sufficient to identify the causal effect.

One potential unmeasured factor is the identity of one's opponent. Many famous chess players, such as Magnus Carlsen, play in these tournaments and an opponents reputation may play a role in the decision to bserserk. Players may also have past experience with specific opponents.
Some additional variables that could be controlled for are:
- Whether a player is playing using an anonymous account.
- The results of previous games between two players.

### Logistic Modelling Assumptions

In this study we used logistic outcome regression to model the effect of berserking on the expected outcome of a chess game. We argued that the players' Elo ratings were the most important factor for both the decision to berserk and the outcome of the game. The Elo system itself is a logit model of player skill, predicting the expected outcome of the game based on the difference in the player's ratings. Because of this, two modelling assumptions were made when Elo ratings were incorporated into the logistic regression:
- The outcome depends only linearly on the players' ratings.
- The outcome depends only on the difference in the rating of the players.

These modelling assumptions are based on the logistic assumptions of the Elo rating system itself. The assumptions of the Elo rating system are well-studied when it comes to chess, and while there is some debate, the consensus is that they are mostly valid in this context. Especially for a tournament where the players are of similar skill. However, it is worth discussing how the model might change if these assumptions are violated.

Because Elo ratings are a logit model, there is an implicit transitivity assumption, e.g. that if player A's expected odds against player B are 10:1 (a 400 point rating difference), and player B's expected odds against player C is 10:1, then A's epected odds against player C are 100:1 (an 800 point rating difference.) This transitivity assumption has been called into question for large differences in rating. In this case higher order terms could be added to the logistic model to compensate.

Another assumption is that the outcome only depends on the relative skill of the players and not the absolute skill, i.e. the model works the same for games between beginners and games between grandmasters. If this assumption is violated, separate terms for each player's ratings may be added to the regression.

## Conclusions

In this project we estimated the negative effect of berserking on a players expected score in a game while controlling for confounding variables.
We found a roughly 150 decrease in performance when a player chooses to berserk.
Future work can consider a larger variety of time controls as well as including a wider range of ratings.
Chess players can immediately put this information into practice when playing in arena tournaments, taking into account the decrease in performance from berserking more precisely when weighing it against the potential gains.
More generally, this gives insights into the nature of time trouble in chess.
Future work may also consider the effects of reduced time at many points in the game rather than just the beginning.
