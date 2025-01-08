Your task this week is to implement the game functionality within the module `game_utils.py`, using **TDD**. 
The file `game_utils.py` provides a skeleton as a starting point.
Note that the functions defined in it need to be implemented, since they are going to be used elsewhere.
However, feel free to define additional helper functions if you think it helps the clarity and readability of your code.

For more context on the code skeleton, see the text page below. 

I've also added some background information on how to structure Python code into modules and packages. 
Note that your projects should have the following structure (`agent_xy` is a placeholder name for now, we'll add concrete examples next week):

```
<projdir>
    |-> agents
    |   |-> agent_xy
    |   |   |-> __init__.py
    |   |   \-> xy_implementation.py
    |   |-> common.py
    |   \-> __init__.py
    |-> tests
    |   |-> __init__.py
    |   \-> test_game_utils.py
    |-> game_utils.py
    \-> main.py
```