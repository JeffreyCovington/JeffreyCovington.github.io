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

## Discussion

Chess players have a lot of experience interpreting differencing in chess ratings.
This analysis suggests that choosing to berserk reduces one's performance by approximately 150 rating points, which should be taken into account when making this choice.
The ultimate goal of this analysis is not just to estimate the effect of berserking, but to estimate the effect of time trouble in general.
It should be noted that these results differ from estimates given by Kenneth Regan, who suggests that reducing one's time by half at this time control results in approximately a 267 point drop in performance.
