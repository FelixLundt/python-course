# Phase 2 information


Your previous agents choose their actions in one of two ways:

- with the minimax algorithm, which traverses the game tree to a fixed depth and uses the heuristically estimated value of future states to make that choice. Its reliance on a handcrafted heuristic means that its performance is determined by the quality of the heuristic, and the number of nodes in the tree it can visit each turn.
- with the MCTS algorithm, which traverses the game tree to variable depth using statistics collected without using any prior knowledge (except the rules of the game). MCTS agents are also limited by the number of nodes that can be visited each turn.

An alternative to these classic approaches is to have the agent learn which actions to choose. There are many ways of accomplishing this, but I will outline a few options below.


###  Organizational matters

Before I provide a little bit of background on how to learn how to play, let me go over what I expect in phase 2. I list two main directions below, but if you have any other ideas I'd be happy to discuss them. I only need to make sure the expected amount of work is roughly the same for each group, and of course that no group gets lost due to an exciting, but way to ambitious idea.

The two main directions are best explained in relation to the agents you've worked on so far. You should aim to push for either

- great performance or
- a powerful algorithm.

I think you can learn the most if you try to optimize one of the two (performance vs. algorithm). You could do this by sticking to the minimax agent (I think that's a more natural choice to optimize compared to MCTS) and try hard to get the most out of it, or by implementing an algorithm that requires offline learning (most likely through self-play). The former is of course conceptually the same as before, but nevertheless an interesting exercise in my opinion, whereas the latter is conceptually almost entirely new, but might be hard to get to work well.

Note that I don't think either of the two is easier, it's really more of a personal preference.


### Using a Policy to Make Decisions

(Sorry, something is broken with the moodle markdown, so the formulas are in plain text instead of rendered LaTeX.) 
The essential problem we're trying to solve is to find a mapping from the set of states S to the set of actions A, known as the policy \pi: S \rightarrow A. Alternatively, one can learn a probabilistic policy over (action, state) pairs \pi: A \times S \rightarrow [0, 1], i.e. \pi(a,s) = \text{Pr}(a_t = a | s_t = s). The simplest implementation of such a mapping would be a lookup table, where the keys are a or (a, s), and the values are s or \text{Pr}(a ∣ s) respectively. 

You'll be unsurprised to hear that this approach is not feasible for the same reason that storing the entire game tree is infeasible: the space A \times S of Connect Four is simply too large. So instead we need to learn a reasonable approximation of $\pi$. 

### Learning \pi

If we want to learn (offline) an approximate policy map, we need something that is good at approximating functions (a function _maps_ inputs to outputs). An obvious candidate would be a neural network, e.g. a multilayer perceptron or a convolutional neural net. Your network should output the probability associated with taking each action available (i.e. the policy) given the current state as input. The next step is to learn the parameters of your function approximator, through some kind of training. The approach you choose should depend on the availability of labelled training data, availability of computational resources, and importantly: your ambition.

### Use a Rich State Representation

The input to your policy net is some representation of the current state. Simply using the board as-is is not optimal. There are a couple of ways to improve the representation. First, instead of representing each location on the board as a single scalar with three possible values, it is preferable to transform each location into three values LOC=(EMPTY, ONE, TWO). LOC=(1,0,0) if the location is empty, (0,1,0) if it is occupied by player one, and (0,0,1) if it's occupied by player two. This is sometimes called a "one hot" encoding. Second, the policy net should learn to avoid illegal actions: In this case, that it cannot play in full columns. In order to learn this, it's helpful to provide a signal that it can associate with this behaviour. This can be achieved by providing seven additional binary inputs, each one indicating whether a column is full.

### Supervised Learning

To learn a policy in a supervised fashion we need a lot of labelled data. In this setting, labelled data simply means you have a set of game boards (the data) each of which has an associated value (the label). That means you need one of the following:

 * Plenty of humans with both the expertise and patience to label your data
 * An oracle (i.e. something that can provide _true_ values for game boards)
 * Some means of estimating the approximate values of your data (i.e. an heuristic)

Though I assume you don't have access to the first option, the good news is that we have the latter two. Obviously, most of you already have an heuristic function. For the oracle though, there is a C++ implementation of a brute-force Connect Four agent that can evaluate \~12,000 positions per second, and can make use of an opening book that should allow it to achieve oracle-like performance, evaluating the true value of boards within milliseconds. You can find its [source code here](https://github.com/PascalPons/connect4). Test your skillz against it [here](https://connect4.gamesolver.org/).

Depending on the complexity of your chosen network, training it may be as simple as performing gradient decent for an objective that minimises its error when predicting the value associated with the actions available from the current board state.

There are also some datasets available on the internet, such as the [UC Irvine set of all 8-ply boards](https://archive.ics.uci.edu/ml/datasets/Connect-4).

### Combining Trained Networks with Tree Search

While a policy network by itself might produce decent results, it is unlikely to result in the strongest possible play. This is because if you can afford to, it is always beneficial to look at future game states. It is therefore crucial to combine the policy net with some kind of tree search, e.g. minimax or MCTS. Such a combination allows you to improve the efficiency of the tree search, by prioritising high-value nodes. 

In the case of minimax, the effectiveness of alpha-beta pruning can be greatly improved by ordering the actions considered according to their corresponding probability as evaluated by the policy net. Importantly, it is also possible to replace your heuristic with a trained "value" network, which takes a board as input and outputs a scalar value (conversely, one can think of your original heuristic as a kind of value network with hand-tuned features!).

For MCTS, a similar sort of substitution is possible. You might consider mimicking AlphaZero by:

 * Traversing the tree (during the Selection phase) according to your policy net by replacing UCT with polynomial upper confidence tree (PUCT)
 * Determining the value of a leaf node by replacing the Monte Carlo roll-out (during the Simulation phase) with a value network's prediction of the board's value (again, the heuristic you wrote is a sort of value net with hand-tuned features)

### Reinforcement Learning

Whether you've trained a policy network using supervised data, or are starting with a randomly initialized network, given sufficient time/resources it is highly advantageous to allow your agent to learn by itself using self-play. We are now way, _way_ outside my area of expertise. So rather than waste your time with my semi-informed ramblings, I'll simply point you to a few relevant resources that I encourage everyone to read.

[General background on AlphaZero](https://towardsdatascience.com/alphazero-chess-how-it-works-what-sets-it-apart-and-what-it-can-tell-us-4ab3d2d08867)

[Lessons From Implementing AlphaZero](https://medium.com/oracledevs/lessons-from-implementing-alphazero-7e36e9054191)

### Learning for a Complicated World

For more complicated games, training your policy and value networks using RL starting with random parameters can make it extremely slow to learn. This is because with randomly initialized networks, the agent will play completely randomly at the outset, making it very slow to discover useful or high-value behavior. But supervised pretraining with data labelled by an oracle or a heuristic may not be feasible due to the complexity of the game. In these cases it can be beneficial to use a technique known as "behavioural cloning". The basic idea is that you use complete records of games played by a competent agent (usually a human) to pretrain your networks in a supervised fashion. This should produce more reasonable behaviour (i.e. competent play) at the beginning of RL-based learning. Using this approach enables RL to more quickly move onto discovering novel (beyond human) strategies. This technique has been used to achieve superhuman performance even in a game as complicated as Star Craft. If you're curious about this, look into AlphaStar.

### Free Compute

If you plan to train large policy or value networks, but don't have access to a powerful GPU or CPU, take a look at these resources:

[Paperspace Gradient](https://gradient.paperspace.com/)

[Google Colab](https://colab.research.google.com/)
