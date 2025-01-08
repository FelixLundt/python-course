## Performance in Python

It is often important to consider the run-time performance of the code you write. While compute resources have become vast and inexpensive, some numerical simulations and analyses can run for hours or even days, so the potential time savings are huge. When playing turn-based games in a competitive setting, the time for players for each move (or the entire game) is usually limited. Therefore, their performance will strongly depend on how much analysis they can run per unit of time.

While the great [Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth) has famously said "Premature optimization is the root of all evil", it does not mean that one shouldn't keep performance in mind from the outset. "Does this code have the potential to run fast?" is a question worth asking yourself throughout the development process. Unfortunately, knowing whether the code you write meets that standard is very difficult, and even a lot of experience sometimes does not save you from being surprised by the performance of your code. Thus, I would advocate the following strategy for writing fast code:

 1. Write your tests.
 2. Write code that is readable and maintainable.
 3. Test for correctness.
 4. Profile the execution of your program.
 5. If it's fast enough, you're done.
 6. Otherwise, optimize the most expensive parts of your program.
 7. Return to step 3.

Tests are crucial in this process, because they allow you to validate all the changes you make in a fast and automated way. After all, optimizing parts of your code necessarily implies making changes.

## Performance in Connect Four

### Profiling Your Agent

Once you have a working agent (that passes all your tests, and appears to play correctly), you can profile the entire agent while it's playing a game. Profiling an entire program provides critical information about where optimization effort will pay the greatest dividends.

Python provides a profiler as part of its standard library called `cProfile` ([see the docs here](https://docs.python.org/3/library/profile.html)). You can profile your agent with the following code (you can replace one of the `generate_move`s below by the `generate_move` function from the random agent if you just want to check briefly):

```python
import cProfile
from agents.agent_minimax import generate_move
from main import human_vs_agent

cProfile.run(
"human_vs_agent(generate_move, generate_move)", "mmab"
)
```

To avoid having to re-run this every time we want to look at the profiling data, we've saved the data to a file called "mmab". We can later inspect the results by using [pstats](https://docs.python.org/3/library/profile.html#module-pstats):

```python
import pstats
p = pstats.Stats("mmab")
p.sort_stats("tottime").print_stats(50)
```

To understand the output of the profiler, I recommend reading its documentation, but here's a copy of the portion that explains the meaning of the measured quantities, by their column headings:

```
ncalls
    for the number of calls.

tottime
    for the total time spent in the given function (and excluding time made in calls to sub-functions)

percall
    is the quotient of tottime divided by ncalls

cumtime
    is the cumulative time spent in this and all subfunctions (from invocation till exit). This figure is accurate even for recursive functions.

percall
    is the quotient of cumtime divided by primitive calls

filename:lineno(function)
    provides the respective data of each function
```

Alternatively, you can use a graphical tool such as [snakeviz](https://jiffyclub.github.io/snakeviz/). Such tools can make it much more intuitive to understand where most of your execution time is spent, and in particular, whether a function is truly "slow" or if most of its execution time is spent calling other functions.

### Microbenchmarking

Once you have data on where your agent spends most of its execution time, you can now begin to optimize those parts. At this step there is now a sub-loop:

 1. Use `timeit` to determine the current per-call run time of the function to be optimized.
 2. Modify the function.
 3. Test for correctness.
 4. Check it again with `timeit`. Is it faster / fast enough?
     * If yes, re-run the profiler and decide if further optimisation is needed.
     * If no, return to step 2 of this sub-loop.

One of the core functions for any Connect Four agent is the one that checks if there are four pieces of the same type connected in a line (we've been calling it `connected_four`). Depending on your implementation of minimax you may have to call this function thousands of times per move, and as such, it is worth considering its performance. Nevertheless, one should first write for readability/maintainability.

In the code below, I've provided two implementations, `connected_four_iter` and `connected_four_convolve`, which are both reasonably easy to understand. Below their implementation I've used the `timeit` module to evaluate their performance. `timeit` is very useful for performing "micro benchmarks", i.e. checking the run-time of a small piece of code.

```python
disable_jit = True
if disable_jit:
    import os
    os.environ['NUMBA_DISABLE_JIT'] = '1'

import timeit
import numpy as np
from numba import njit
from scipy.signal.sigtools import _convolve2d
from game_utils import connected_four, initialize_game_state, BoardPiece, PlayerAction, NO_PLAYER, CONNECT_N

@njit()
def connected_four_iter(
	board: np.ndarray, player: BoardPiece, _last_action: Optional[PlayerAction] = None
) -> bool:
    rows, cols = board.shape
    rows_edge = rows - CONNECT_N + 1
    cols_edge = cols - CONNECT_N + 1

    for i in range(rows):
        for j in range(cols_edge):
            if np.all(board[i, j:j+CONNECT_N] == player):
                return True

    for i in range(rows_edge):
        for j in range(cols):
            if np.all(board[i:i+CONNECT_N, j] == player):
                return True

    for i in range(rows_edge):
        for j in range(cols_edge):
            block = board[i:i+CONNECT_N, j:j+CONNECT_N]
            if np.all(np.diag(block) == player):
                return True
            if np.all(np.diag(block[::-1, :]) == player):
                return True

    return False


col_kernel = np.ones((CONNECT_N, 1), dtype=BoardPiece)
row_kernel = np.ones((1, CONNECT_N), dtype=BoardPiece)
dia_l_kernel = np.diag(np.ones(CONNECT_N, dtype=BoardPiece))
dia_r_kernel = np.array(np.diag(np.ones(CONNECT_N, dtype=BoardPiece))[::-1, :])


def connected_four_convolve(
	board: np.ndarray, player: BoardPiece, _last_action: Optional[PlayerAction] = None
) -> bool:
    board = board.copy()

    other_player = BoardPiece(player % 2 + 1)
    board[board == other_player] = NO_PLAYER
    board[board == player] = BoardPiece(1)

    for kernel in (col_kernel, row_kernel, dia_l_kernel, dia_r_kernel):
        result = _convolve2d(board, kernel, 1, 0, 0, BoardPiece(0))
        if np.any(result == CONNECT_N):
            return True
    return False


board = initialize_game_state()

number = 10**4

res = timeit.timeit("connected_four_iter(board, player)",
                    setup="connected_four_iter(board, player)",
                    number=number,
                    globals=dict(connected_four_iter=connected_four_iter,
                                 board=board,
                                 player=BoardPiece(1)))

print(f"Python iteration-based: {res/number*1e6 : .1f} us per call")

res = timeit.timeit("connected_four_convolve(board, player)",
                    number=number,
                    globals=dict(connected_four_convolve=connected_four_convolve,
                                 board=board,
                                 player=BoardPiece(1)))

print(f"Convolve2d-based: {res/number*1e6 : .1f} us per call")

res = timeit.timeit("connected_four(board, player)",
                    setup="connected_four(board, player)",
                    number=number,
                    globals=dict(connected_four=connected_four,
                                 board=board,
                                 player=BoardPiece(1)))

print(f"My secret sauce: {res/number*1e6 : .1f} us per call")
```

If I run the above code, I get the output:

```
Python iteration-based:  419.0 us per call
Convolve2d-based:  44.0 us per call
My secret sauce: <this should be your implementation's run-time>
```

While the `convolve2d`-based approach is initially (an order of magnitude) faster than our for loop-based approach, try setting `disable_jit` (on the first line) to `False` which will enable Just-in-time (JIT) compilation of the `connected_four_iter` function by [numba](https://numba.pydata.org/). Here are the results when I do just that:

```
Python iteration-based:  7.8 us per call
Convolve2d-based:  39.3 us per call
My secret sauce: <this should be your implementation's run-time>
```

### Micro Benchmark Caveat

When performing micro benchmarks like these, one crucial point to bear in mind is that run-time can sometimes be significantly affected by the data your code is operating on. For example, consider what happens to run time if there is four connected pieces on the first row of the board (`board[0, :4] = PLAYER1`):

```
Python iteration-based:  0.5 us per call
Convolve2d-based:  23.0 us per call
```

As such, it is often worth finding a way to provide a range of different inputs to the function you are benchmarking. In principle, the idea is to approximate the input statistics your code would encounter in the wild'. This is also one of the reasons why just micro-benchmarking is not enough, you always have to profile the entire program to check for real-world implications of changes you make. 