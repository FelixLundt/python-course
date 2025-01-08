## Structuring your Python Project

The structure of a project can seriously affect your ability to write good code. Consequently, for developing our agents I recommend the project be structured like so:

```
<projdir>
    |-> agents
    |   |-> agent_xy
    |   |   |-> __init__.py
    |   |   \-> xy_implementation.py
    |   |-> common.py
    |   |-> __init__.py
    |-> tests
    |   |-> __init__.py
    |   \-> test_game_utils.py
    |-> game_utils.py
    \-> main.py
```

The `game_utils.py` module is the one you will be working on this week. It is going to contain code of general relevance for playing the game and for the agents you will implement. They are going to be put into their own `agents` package, with one subpackage per agent. For illustration, I've added a subpackage for a minimax agent already, but you can ignore this for now. The module `common.py` in the `agents` package will be the place for code that all your agents use, but is not necessary for general game play functionality. We'll discuss this in more detail in the coming weeks. The `main.py` file is the main script to be run when you want to play a game against the agent(s). 


## Week 2 Task

Your task this week is to implement the game functionality within the module `game_utils.py`, using **TDD**. 
The file `game_utils.py` provides a skeleton as a starting point.
Note that the functions defined in it need to be implemented, since they are going to be used elsewhere.
However, feel free to define additional helper functions if you think it helps the clarity and readability of your code.


## Test-driven development

You're going to be doing [test-driven development (TDD)](https://en.wikipedia.org/wiki/Test-driven_development). Consequently, you need to devise and implement appropriate tests for all those functions, __prior__ to writing them.

Testing, especially when done before actually implementing your cool, new ideas, often seems like a distraction. Writing new code can be fun and rewarding, while writing tests---after the fact---feels like an annoying responsibility (like cleaning up after the party). Fortunately, when using TDD, testing makes writing & maintaining code easier, more effective, and less bug-prone. Without proper testing it is possible to waste weeks investigating "scientific results" that ultimately turn out to be nothing but a programming error (this happens __a lot__).

The advantages to TDD are manifold: 

 * Having to write tests first forces you to think carefully about how a function should behave.
 * Once all tests of a function pass, you can be confident your implementation is correct.
 * Refactoring and optimisation often introduce subtle bugs. Running your tests after making changes will reduce these errors.

Effective testing relies on careful thought about the desired behaviour of your functions. What input values/types are valid, and what is the corresponding output? What should you do about invalid inputs? Think about special cases: [Edge/Boundary](https://en.wikipedia.org/wiki/Edge_case), [Corner](https://en.wikipedia.org/wiki/Corner_case), and [Base](https://en.wikipedia.org/wiki/Base_case) cases.

## PyTest

For testing, I recommend using the [pytest](https://docs.pytest.org/en/latest/) framework. It's worth spending some time to look at the documentation! 

For PyCharm integration to work correctly, see [here](https://www.jetbrains.com/help/pycharm/pytest.html).
In VS Code, follow the steps in this [guide](https://code.visualstudio.com/docs/python/testing#_test-configuration-settings).

Since the functions you are to implement are in `game_utils.py`, you should put their tests in `tests/test_game_utils.py`. For illustration purposes only, here's an example of a very simple test of `initialize_game_state`. This is just meant to demonstrate the basic structure and to give you something to run, but NOT a template for a test I'd actually recommend (mainly because of several assertions in one test function). 

```python
import numpy as np
from agents.game_utils import BoardPiece, NO_PLAYER

def test_initialize_game_state():
    from agents.game_utils import initialize_game_state

    ret = initialize_game_state()

    assert isinstance(ret, np.ndarray)
    assert ret.dtype == BoardPiece
    assert ret.shape == (6, 7)
    assert np.all(ret == NO_PLAYER)
```

If you enabled `pytest` support in Pycharm or VS Code, you will notice a green arrow appears to the left of any test functions in that file (click on the arrow to run just that test). Please check the documentation for all ways to run your tests, and take advantage of the fact that you can run all tests in a test module at once.



## Type hints

The first thing to know is that Python is a [dynamically typed language](https://en.wikipedia.org/wiki/Type_system#Dynamic_type_checking_and_runtime_type_information). More specifically, Python uses something known as [duck typing](https://en.wikipedia.org/wiki/Duck_typing). In brief, an operator (function) can be applied to any set of operands (objects) so long as the operands have certain necessary properties. An example might help:

```python
def foo(a, b):
    return a + b

foo(1, 2)
# Output: 3

foo('x', 'y')
# Output: 'xy'

foo(1, 'y')
# Output: TypeError: can only concatenate str (not "int") to str
```

The function `foo` is indifferent to the type of its arguments `a` & `b`. The only thing that matters is that there is some sensible way in which to perform an addition operation on the two of them. Here's another example:

```python
def bar(name):
    return "Hello " + name

bar("Sue")
# Output: "Hello Sue"

bar(123)
# Output: TypeError: can only concatenate str (not "int") to str
```

It would be useful to provide a hint to ourselves, and any user of the function `bar` that it can only accept an argument of type string, and that it returns a string:

```python
def bar(name: str) -> str:
    return "Hello " + name
```

Obviously in this example it is trivial to determine this simply by looking at the code. But for more complicated functions, it can become highly non-trivial to determine exactly what types are permitted, and the return type. Furthermore, when working in a good editor, those type hints make autocompletion (when the editor makes suggestions to you) more effective, and can permit the editor to warn you when you're doing something wrong.

Returning to our function `foo`, we can also improve this function a bit using type hints:

```python
from typing import TypeVar
T = TypeVar('T')

def foo(a: T, b: T) -> T:
    return a + b

foo(1, 'x')
# PyCharm warning: Expected type 'int' (matched generic type 'T'), got 'str' instead
```

That code now informs the programmer, and the IDE, that the type of `a` and `b` must match, and that the return value has the same type as the arguments. Please note that this example is merely meant to be educational. This approach (and type hinting in general) will not keep you from shooting yourself in the foot, i.e. if the arguments have the same type, but there is no way to add them, the program will still fail at run time (with an error like `TypeError: unsupported operand type(s) for +: 'A' and 'A'`, if the arguments are of type `A`).

## On enumerations

Enumerations are widely used in programming because it is common to want to specify that one of a finite set of conditions is true. So take GameState as an example: Either the last move has resulted in a win, a draw, or the game is still being played. Now how should one specify this? One could return a string ("WIN", "DRAW", "STILL_PLAYING"), but this solution is error-prone and requires recalling these "magic strings", or looking up the set of valid ones. Every time you check the value of the game state, you risk introducing a bug if you slightly mis-type that string. OK, so strings are bad, what about defining "magic integers", i.e.
```python
WIN = 1
DRAW = -1
STILL_PLAYING = 0
```
That is certainly better than the strings, and is basically what the enum class does, except that by using an enum, you specify in one location the complete set of accepted values. This makes code autocompletion extremely effective for discovering those values. And that's basically it. Enums aren't super sexy or exciting, but they've been one of the bread-and-butter features of programming languages since time immemorial and should definitely be in the tool-belt of every competent programmer.