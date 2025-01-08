This week, we'll discuss basics of version control using Git, introduce Minimax and MCTS as algorithms to play the game, and add additional functionality to our project. 

There are a few assignments for this week:

- Put your project under version control using Git (see links in material) and GitHub
- Add code snippets (see material) and make sure you understand them.
- Implement an agent playing randomly (more info in material).
- Start to implement your agent using Minimax/Negamax (with alpha-beta pruning) or MCTS



Using the code snippets in the material below, you're project should look like this (before you start working on your minimax/MCTS agent):

```
<projdir>
    |-> agents
    |   |-> agent_random
    |   |   |-> __init__.py
    |   |   \-> random.py
    |   |-> agent_human_user
    |   |   |-> __init__.py
    |   |   \-> human_user.py
    |   |-> common.py
    |   |-> __init__.py
    |-> tests
    |   |-> __init__.py
    |   \-> test_game_utils.py
    |-> game_utils.py
    \-> main.py
```