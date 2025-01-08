### Debugging

Debugging your code can be a substantial time sink, so it's important to know how to do it well. An obvious, but difficult first step is of course to avoid mistakes in the first place, and I think TDD is a good way to do so. The incremental workflow of TDD means you always know everything worked a single, small change ago, so you almost always know where to look for bugs immediately.
In all other cases, when you don't know exactly where the problem is, you have almost no other choice than to explicitly look at what your code is doing. The easiest way to do this is to insert print statements, in order to check on variables etc. at various places throughout your code. This has a few problems though:

- Inserting print statements means you change your code, and you will have to remove the print statements again once you're done.
- You need something that runs the specific piece of code you want to debug. Often, using it in the full context of the project is annoying, since a lot of other things are going to be executed as well. However, writing little programs that only run one particular function means you produce even more code that you don't have any use for anymore once the bug is fixed.
- You need to decide where to put the print statements. Either you insert a lot of them (meaning a lot of changes, a lot of stuff printed to the console), or you have to make a guess (which is likely going to be wrong, since you don't know what the problem is - so you'll have to try multiple times). 

To get around these issues, I strongly recommend using the debugger that comes with your IDE. It allows you to examine what your code is doing line-by-line in a very controlled way. You'll have full access to all variables, so you can not only passively see what is happening, but actively act on the state of the program as well. If you are looking for bugs in a function, the best thing to do is to debug the tests for it (i.e., start the debugger in a test and then step into the implementation of the function under test). That way, all of the three issues for print statements outlined above are addressed. And as a bonus, you'll be able to debug the test as well while you're at it.

In order to get started, I recommend checking the documentation of your IDE. PyCharm's debugger requires less setup, and the documentation contains a nice and illustrative example. I recommend checking out PyCharm's documentation even if you use another IDE, since a lot of the concepts carry over. You'll get started [here with this official introduction](https://www.jetbrains.com/help/pycharm/part-1-debugging-python-code.html). Once you've read that very basic guide, you might consider reading [the more extensive documentation](https://www.jetbrains.com/help/pycharm/debugging-code.html). Pay attention to the keyboard shortcuts, because using them will make you vastly more efficient. Note that the keyboard shortcuts are OS specific, so be sure to select your OS in the dropdown at the top of the documentation website (if you're using Ubuntu, choose GNOME).

Particularly important sections of the documentation are:

 * [Breakpoints](https://www.jetbrains.com/help/pycharm/using-breakpoints.html)
 * [Examine suspended program](https://www.jetbrains.com/help/pycharm/examining-suspended-program.html)
 * [Evaluating Expressions](https://www.jetbrains.com/help/pycharm/evaluating-expressions.html)
 * [Step through the program](https://www.jetbrains.com/help/pycharm/stepping-through-the-program.html)
 * [Viewing as Array or DataFrame](https://www.jetbrains.com/help/pycharm/viewing-as-array.html)

In VS Code, there is a plethora of settings you can tweak, but the debugger should work quite well out of the box. I recommend skipping the settings as far as possible for the beginning. You find the documentation and a tutorial [here](https://code.visualstudio.com/docs/python/debugging) and [there](https://code.visualstudio.com/docs/python/python-tutorial#_configure-and-run-the-debugger).
