## Explanation of variables:
- $\mathrm{Outcome}$ is the outcome of the game from White's perspective. It takes the values 0, 1, 2 for a loss, draw, or win for White respectively.
- ${\mathrm{Rating}}$ is the Glicko 2 rating for White (W) and Black (B). A rating is typically between 0 and 4000 and rounded to the nearest integer.
- ${\mathrm{Streak}}$ encodes whether White (W) and Black (B) are on a streak, affecting the relative value of a win in the tournament standings. After winning two games in a row, a player earns double points for a win. A streak is broken after a draw or loss. This is encoded as 0, 1, 2 for no streak, 1 win streak, and 2+ win streak respectively.
- $\mathrm{NoDraw}$ represents whether White (W) and Black (B) are averse to making a draw. This is an unobserved variable as it represents the player's state of mind. While the outcome of a normal chess game is zero-sum, in the context of a tournament players may view a draw as worth less. 
- $\mathrm{Berserk}$ represents whether White (W) and Black (B) choose to berserk, i.e. cut their starting time in half for the opportunity of scoring an extra point. This is the variable that will be intervenned on.
- $\mathrm{TC}$ is the time control, i.e. the initial allotment of time for each player.

## Proof of Identification

![The DAG for this setup.](/dag.png)
