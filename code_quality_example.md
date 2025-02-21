### Improving code quality - Example

One of the main goals of this course is to help you write clean, testable (and tested), maintainable, shareable code.
In the beginning of the semester, we went over properties that can serve as proxies for these qualities, as well as 'code smells' that can indicate that there might be issues (see TDD demo). 

Below, you'll find two exemplary implementations of the `pretty_print_board` function to illustrate what that can look like. Before we start, let me point out that the `pretty_print_board` function has a very narrow purpose and is basically only used when running the game as in `main.py`. Therefore, I think the benefits from improving the implementation as below are limited and should not be your first priority. Work on the implementation of your agent first and try to make its implementation as nice as you can, and only then look at utility functions. 
I only chose this function as an example since it's something everybody has implemented, not because of its importance.

##### Straightforward, but flawed implementation

Here's one way to implement the function:
```python
import numpy as np
from game_utils import NO_PLAYER_PRINT, PLAYER1_PRINT, PLAYER2_PRINT


def pretty_print_board(board: np.ndarray) -> str:
    board_string = ""
    for x in range(6):
        temp_row_string = ""
        for y in range(7):
            if board[x, -y] == 0:
                temp_row_string += NO_PLAYER_PRINT + " "
            elif board[x, -y] == 1:
                temp_row_string += PLAYER1_PRINT + " "
            elif board[x, -y] == 2:
                temp_row_string += PLAYER2_PRINT + " "
        temp_row_string = "|" + temp_row_string[:-1] + "|\n"
        board_string = temp_row_string + board_string

    board_string = "\n|=============|\n" + board_string
    board_string += "|=============|\n|0 1 2 3 4 5 6|"
    return board_string
```
By going through the implementation line by line, I'm sure it's going to be pretty clear what the function is doing. It's relatively easy to write tests for it to make sure it works, and I think leaving it as it is in favor of spending time on other parts of the project would be a reasonable trade-off.

However, there are a few things that could be improved:

- Bad naming: Names like `x` and `y` are not very expressive. It takes a moment to realize what they represent and to make sure everything is consistent.
- Complicated structure: There are two nested loops with conditionals inside them. Of course it's not hard to see what they do in this case, but this is not the simplest way to communicate what is happening.
- Hard-coded values: The loops' ranges are just bare numbers, instead of using `BOARD_COLS` and `BOARD_ROWS`. The same is true for the values of the pieces on the board (the variables of type `BoardPiece`).
- It would be even better to just iterate over the board array directly (i.e., `for row in board[::-1, :]; for piece in row`), instead of using the two loops.
- The delimiter line `|=============|\n` should be a variable, which can be reused, instead of being repeated.
- The updates to the variable `board_string` in lines 31 and 32 are inconsistent and could be put in one line. This should be a simple 'put everything together line', but it doesn't read that way.


##### Better implementation

The issues pointed out above are all fixed in the solution below:

``` python
def pretty_print_board(board: np.ndarray) -> str:
    delimiter_line = '|=============|\n'
    numbered_line = '|0 1 2 3 4 5 6|'

    lines = [_pretty_print_board_line(board_row) for board_row in board[::-1, :]]
    ret_str = delimiter_line + ''.join(lines) + delimiter_line + numbered_line

    return ret_str

def _pretty_print_board_line(board_row: np.ndarray) -> str:
    def board_pos_str_converter(elem: np.int8) -> str:
        if elem == PLAYER1:
            return PLAYER1_PRINT
        elif elem == PLAYER2:
            return PLAYER2_PRINT
        else:
            return NO_PLAYER_PRINT
    line = '|'
    for piece in board_row:
        line += board_pos_str_converter(piece) + ' '
    line = line[:-1] + '|\n'  # remove last space and add newline
    return line
```

Note that the heavy lifting is done by splitting the function up. That way, the nitty-gritty details of the conversion are hidden away in the function `_pretty_print_board_line` (note that the underscore indicates this function shouldn't be used on its own), while the remaining code in `pretty_print_board` communicates what is happening on a higher level very clearly.

As a final remark, note that using TDD leads to implementations like this really naturally. It's much easier to start with tests for the `_pretty_print_board_line` function, since you wouldn't have to worry about the overall board size and additional fluff.
After having tested the helper function thoroughly, I'd argue one more test for the `pretty_print_board_function` would be enough just to make sure the overall string is put together correctly.

