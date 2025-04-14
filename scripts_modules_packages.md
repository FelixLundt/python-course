## Python scripts, modules and packages

#### Interactive shell

It possible to work with Python in several different ways. The first is interactively: You can use Python's interactive shell [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) to type in Python code and excecute it on the [interpreter](https://en.wikipedia.org/wiki/Interpreter_(computing)), see the result, enter more code, and so on. Once you close the shell, all of your code and results are gone. Obviously this is not ideal for developing complicated programs. 

#### Scripts & Modules
The second is to put your code into a `script` (in Python this is any file with a `.py` file extension that has executable code in it). As your program becomes larger, you might want to split it into multiple files. This is where the idea of a `module` comes in. If you have two scripts, say `script_a.py` and `script_b.py` that both rely on some common functionality, you might refactor that code into a module called `common.py`. 

The main distinguishing characteristic of a module is that running it directly (i.e. with the command `python common.py`) typically does nothing other than define some things, such as classes and functions (this is not a strict rule: there are exceptions to this, but we will ignore them for now), while scripts typically do something useful like print information to the terminal, run simulations or process and store data.

Once you have a module, you can easily access the shared functionality it contains. In our example, given a directory structure like so
```
Project directory
    |-> common.py
    |-> script_a.py
    \-> script_b.py
```

with this as the content of `common.py`:
```python
def foo():
    print("Help me, I was refactored, and now I'm stuck in the common module!")
```

We can use the content of `common.py` in the scripts in the directory. This might be how `script_a.py` does that:
```python
import common
print(common.foo())
# Output: "Help me, I was refactored, and now I'm stuck in the common module!"
```
Alternatively, this might be how `script_b.py` looks:
```python
from common import foo
print(foo())
# Output: "Help me, I was refactored, and now I'm stuck in the common module!"
```

#### Packages

So what happens if you project gets larger still, and you don't want 3000 module files in your project directory? You can start organizing your modules into `packages`. In Python, the traditional definition of a package is simply a directory that has an `__init__.py` file in it. This is [no longer strictly necessary](https://www.python.org/dev/peps/pep-0420/), but I would nevertheless recommend following this practice. The `__init__.py` file is the first thing that gets executed when you import a package. 

Let's look at an example where I don't define an init file:

```
Project directory
    |-> my_first_package
    |   \-> common.py
    \-> script.py
```

Let's change our function in `common.py` to reflect the new location:
```python
def foo():
    print("Oh no! Now it's even worse: My module got stuck in a package!")
    ```

If I want to access functionality defined in the module `common`, I now have to import it from `my_first_package`. Our script `script.py` could do this as follows:
```python
from my_first_package import common
print(common.foo())
# Output: "Oh no! Now it's even worse: My module got stuck in a package!"
```

``` python
from my_first_package.common import foo  # You can also do this.
```

So what if we don't want to worry about where in the package `foo` is defined? We can add an `__init__.py` file to the `my_first_package` directory, with the following code:
```python
from .common import foo
```
Now we can import `foo` like so:
```python
from my_first_package import foo
```

What just happened there? When Python first encounters a package within an import statement (including if you're importing a module contained within the package), the first thing the interpreter does is run the `__init__.py` file in that package. The fact that `foo` was imported into the namespace of the init file means that it's accessible from the namespace of the package.

## Import statements

This brings us to the topic of import statement syntax. In general, there are four different kinds of import statements:

 1. `import <package>`
 2. `import <module>`
 3. `from <package> import <module or subpackage or object>`
 4. `from <module> import <object>`

For all four of these, you can use the `as` syntax to create an alias for the import (`import x as y`, `from a import b as c`).

This finally brings us to everyone's favourite topic: absolute vs. relative imports. Similar to how paths work in a file system, you can specify the location of a package/module either in absolute terms (in file system terms, this means specifying a path starting in the root directory, e.g. `/var/lib`), or relative to the location of the module doing the import (e.g. `./lib` if my shell is currently in the `/var` directory).

Relative imports are only permitted using the `from x import y` syntax, as in `from .<module/package> import y`, where `<module/package>` is prefixed by a single dot to indicate the current package. Two dots indicates one package above (similar to how `cd ..` moves you up one directory in a file system), three dots two packages above, etc. I've already used a relative import in `from .common import foo`. This says import `foo` from the module `common`, which is in the directory that this file is in.
