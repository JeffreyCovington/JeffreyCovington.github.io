
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