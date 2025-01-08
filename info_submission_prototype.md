##### Main goal

The main goal for the prototype is the following:

- A functioning, correctly implemented agent using either minimax or MCTS (alpha-beta pruning would be great, but isn't essential)
- Clean, readable, maintainable code
- Tested code
- Documented code


The prototypes are also a great opportunity to give you feedback on your code, which you can use in the coming weeks for your more advanced agents. 

##### Details

Important for submission:

-  Due date: **Sunday, November 17**,  23:59 pm.
- Please submit your zipped project directory (i.e., `agents` and `tests` packages as well as functioning `main.py` script and anything else necessary to use your agents, but no virtual environments etc.). Please make sure `main.py` works, the first thing I do is to play against the agents and I don't want to have to change anything in order to do so. If you made non-obvious changes to my version of the script, please let me know by commenting the submission. 


##### Grading

Below, I'll first list what the criteria for the scores are and how they're weighted (percentages are definitive now), and then list a few things I pay special attention to.

**Criteria Minimax Agent:**

| Criterion |  Weight  |  Comment |
| ----------- | ----------- | ------------|
| Test quality & coverage | 26% |  Are tests specific enough and cover all relevant cases? |
| Code quality & readability | 26% |  Attributes of good code (see TDD demo) & project structure |
| Documentation | 14% | Docstrings for classes & functions, proper use of comments*, type hints. |
| Algorithm implementation | 26% | Algorithms correctly and efficiently implemented?** | 
| Quality of play | 8% |       |

*Proper in the sense of as few as possible, as many as necessary/helpful.
**This does not imply any strict performance criterion. Efficiently here means that there aren't unnecessary extra steps in your code, for example, or different parts contradicting one another. However, it would be great if you set up your agent such that it finds a move in about 5 seconds (with an empty board).

- Please note that the categories overlap to some degree. The 'Quality of play'-category is more of a bonus.
- Please don't submit just a copied version of minimax/negamax or MCTS pseudocode (e.g., from Wikipedia) translated into Python. It's perfectly fine to take inspiration from it to get an idea of how to implement your agent, but please try to come up with your own design. Keep the design principles/attributes of good code in mind (see TDD demo). The very minimum would be to take a few extra refactoring steps.



##### Final remarks

- In order to improve code quality & readability, think about possibilities to refactor with the typical [code smells](https://blog.codinghorror.com/code-smells/) in mind. Of those, especially important to me are
    - inconsistent/uninformative names
    - long methods/functions
    - duplicated & near-duplicated code
    - lots of if/else blocks
    - multiple return paths in functions/methods
- As already expressed in the tables, docstrings in functions and classes (including methods) are enough. Those should look like shown [here](https://realpython.com/documenting-python-code/). If you don't follow Google's or NumPy/SciPy's format for docstrings, please let me know which one you're using. I personally recommend both using type hints and indicating the type again in the docstring of a function/method, but I'm okay with only type hints. 