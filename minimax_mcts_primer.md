While Connect 4 is a [solved game](http://www.informatik.uni-trier.de/~fernau/DSL0607/Masterthesis-Viergewinnt.pdf), it nevertheless provides an interesting platform to learn the basics of algorithmic game-play.

The agent you implement will use some variant of the [Minimax algorithm](https://en.wikipedia.org/wiki/Minimax) or the [Monte Carlo tree search algorithm](https://en.wikipedia.org/wiki/Monte_Carlo_tree_search) (MCTS). Both algorithms achieve this by looking at possible moves that might be taken in the future by itself and its opponent. This set of future game-states forms a tree, and so Minimax is essentially a tree search algorithm.

On its face, this appears to be a pretty straightforward problem: Just exhaustively build the game tree, and only choose actions that lead to wins. Unfortunately, for a 6x7 board the number of possible board configurations is... large (4,531,985,219,092 to be precise). If you somehow stored this tree using a single byte per board, it would take over 4,500 GB of memory.

## Minimax with alpha-beta pruning

The solution that Minimax-based approaches typically employ is to evaluate the game tree down to some fixed depth (say, 4 moves into the future), and then assigns a value/cost to that board state by applying a [heuristic](https://en.wikipedia.org/wiki/Heuristic_(computer_science)). It then attempts to maximize the possible 'reward' and minimize the possible 'loss' for a worst-case scenario (i.e. it assumes the opponent will be trying to create a worst-case scenario for you, each time it makes a move). For simplicity, we do not consider scenarios where the opponent uses a different heuristic.

While naïve Minimax is an effective solution to the problem of choosing your agents next action, it is not terribly efficient. To see why, consider a Minimax algorithm that looks two moves into the future (its own move, then its opponent's response), and how it would evaluate the following mini-board:
```
  X
O OO
X XO
====
0123
```
The agent is playing `X`. It begins by considering its opponents possible responses to it playing in column 0 (the game sub-tree). None of the responses lead to an immediate end state, so it assigns a cost to each board resulting from those responses. The cost then of playing column 0 is taken to be the maximum of those costs (since it is assumed the opponent will choose the worst-case outcome for our agent). Let's imagine that cost is 5.

The agent next considers the game sub-tree that results from playing in column 1. It will soon see that its opponent will immediately win by responding in column 1. Surely this cost is higher than 5 (the worst-case cost of playing column 0). Standard Minimax will continue to consider all of its opponents other potential responses (i.e. what happens if the opponent plays column 2 or 3), despite the fact that playing in column 1 will clearly lead to a very bad outcome. This additional computation is unnecessary, and can be skipped because playing column 1 results in a worse worst-case than playing in column 0.

Eliminating this inefficiency is what [alpha-beta pruning](https://en.wikipedia.org/wiki/Alpha%E2%80%93beta_pruning) does.

Finally, instead of Minimax, you might consider implementing [Negamax](https://en.wikipedia.org/wiki/Negamax).

### Monte Carlo Tree Search

Strictly speaking, [Monte Carlo tree search](https://en.wikipedia.org/wiki/Monte_Carlo_tree_search) (MCTS) is a heuristic search algorithm. But I think it is instructive to think of it as a sort of "online learning" algorithm, because as it runs it "learns" to estimate the value of actions in a given state by sampling the state space (in fact, under certain conditions, it converges to minimax with infinite depth). The sampling process has a deterministic component that balances exploration and exploitation (the selection phase), and a stochastic component (the simulation/roll-out phase). MCTS has the further advantages that it neither requires offline training, nor a heuristic/evaluation function, meaning that no domain expertise is required (outside of knowing which actions are legal in a given state, and the conditions that result in the end of the game).

I recommend you read [this page](https://medium.com/@quasimik/monte-carlo-tree-search-applied-to-letterpress-34f41c86e238) for a more complete explanation of how MCTS works.
