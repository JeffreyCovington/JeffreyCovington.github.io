# Discussion of Assumptions and Sensitivity Analysis

It is well known that time trouble has a significant effect on the outcome of a chess game. Having less time to make decisions leads to poor decision making. This is backed up by the collective experience of the chess community. Then the question needed to answer is what the magnitude of the effect is.

To investigate this we can look at data from lichess.org, namely the results of their the arena-format tournaments. Because players can choose to "berkserk", i.e. reduce their initial time by half for the chance to score more points, this can provide valuable data on the effect of time trouble. By controlling for the Elo rating of the players and their tournament standings, we found that, on average, choosing to berserk reduced one's performance by approximately 150 rating points, for an equivalent expected score of 29% against an equal opponent.

However, the decision to berserk or not comes down to the individual psychology of the players, which is rife with potential unmeasured confounding factors. In this section we will examine the psychological and modelling assumptions used in this study.

## Player Psychology and Unmeasured Confounding

Our data only included tournaments from the lichess.org Titled Arenas: tournaments with substantial cash prizes that are only open to professional players. Because of this we made the assumption that players are acting in their own self interest when making the decision to berserk or not based on their experience and the information available to them.

However, players have two distinct interests in the context of an arena tournament: doing well in an individual game and doing well in the tournament. For most games these interests are aligned, but chess allows for draws. A draw is a neutral result for the game but can be a negative result for the tournament standings by breaking a players winning streak (which awards extra points for consecutive wins.)

As such we focused on two confounding factors that affect the outcome of the game: the relative skill of each player and each players' desire to avoid a draw. Both of these factors are reflected the information available to each player: the relative Elo rating of the players and the winning streak of each player. Under the causal assumptions used in the proof of identification, we showed that controlling for these measured variables were sufficient to identify the causal effect.

One potential unmeasured factor is the identity of one's opponent. Many famous chess players, such as Magnus Carlsen, play in these tournaments and an opponents reputation may play a role in the decision to bserserk. Players may also have past experience with specific opponents.
Some additional variables that could be controlled for are:
- Whether a player is playing using an anonymous account.
- The results of previous games between two players.



## Logistic Modelling Assumptions

In this study we used logistic outcome regression to model the effect of berserking on the expected outcome of a chess game. We argued that the players' Elo ratings were the most important factor for both the decision to berserk and the outcome of the game. The Elo system itself is a logit model of player skill, predicting the expected outcome of the game based on the difference in the player's ratings. Because of this, two modelling assumptions were made when Elo ratings were incorporated into the logistic regression:
- The outcome depends only linearly on the players' ratings.
- The outcome depends only on the difference in the rating of the players.

These modelling assumptions are based on the logistic assumptions of the Elo rating system itself. The assumptions of the Elo rating system are well-studied when it comes to chess, and while there is some debate, the consensus is that they are mostly valid in this context. Especially for a tournament where the players are of similar skill. However, it is worth discussing how the model might change if these assumptions are violated.

Because Elo ratings are a logit model, there is an implicit transitivity assumption, e.g. that if player A's expected odds against player B are 10:1 (a 400 point rating difference), and player B's expected odds against player C is 10:1, then A's epected odds against player C are 100:1 (an 800 point rating difference.) This transitivity assumption has been called into question for large differences in rating. In this case higher order terms could be added to the logistic model to compensate.

Another assumption is that the outcome only depends on the relative skill of the players and not the absolute skill, i.e. the model works the same for games between beginners and games between grandmasters. If this assumption is violated, separate terms for each player's ratings may be added to the regression.
