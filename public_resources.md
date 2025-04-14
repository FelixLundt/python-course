#### Must reads

- [Clean Code Textbook](https://www.pearson.com/en-us/subject-catalog/p/clean-code-a-handbook-of-agile-software-craftsmanship/P200000009044/9780132350884) by Bob Martin
- [Test Driven Development by Example](https://www.oreilly.com/library/view/test-driven-development/0321146530/) by Kent Beck

Both of those are standard references and I strongly encourage you to read them. They are hugely insightful and have helped me a lot personally.

#### Allround resources

- [Python Textbook](https://python-textbok.readthedocs.io/en/1.0/index.html) (general Python intro, object-oriented programming, testing, packaging)
- [MolSSI page](https://education.molssi.org/python-package-best-practices/) about Python best practices (Packaging, imports, testing, documentation)
- [Book](https://goodresearch.dev/index.html) on software carpentry for scientists (coding style, documentation, testing)
- [Course](https://merely-useful.tech/py-rse/) on software development for researchers (Unix shell, Git, testing, packaging)
- YouTube channels by [Arjan Codes](https://www.youtube.com/@ArjanCodes), [James Murphy](https://www.youtube.com/c/mCodingWithJamesMurphy) and [Corey Schafer](https://www.youtube.com/channel/UCCezIgC97PvUuR4_gbFUs5g) for Python in general (Tips & Tricks, Object-Oriented Programming, ...)


#### Software/Tools

##### IDEs
- [VS Code](https://code.visualstudio.com/)
- [PyCharm](https://www.jetbrains.com/pycharm/) IDE (professional version is free for students)

##### Python
- [Anaconda](https://www.anaconda.com/products/distribution) (Python distribution including the [conda](https://docs.conda.io/projects/conda/en/latest/index.html#) package & environment manager)

#### Virtual environments/dependency management
- [`virtualenv`](https://virtualenv.pypa.io/en/latest/)
- [`uv`](https://docs.astral.sh/uv/)



##### Connect 4
- Site to [play C4](https://connect-4.org/en)

##### Python & Numpy
- General Python & Numpy intros
     - [Material](https://swcarpentry.github.io/python-novice-inflammation/) from neuromatch summer school (Python)
     - [DataScienceHandbook](https://jakevdp.github.io/PythonDataScienceHandbook/) with notebooks (Python & Numpy)
     - [Notebooks from Hands on Machine Learning Book](https://github.com/ageron/handson-ml2/blob/master/tools_numpy.ipynb) (Numpy)

- Packages, modules, imports
   - [Article 1](https://learnpython.com/blog/python-modules-packages-libraries-frameworks/) and [Article 2](https://realpython.com/python-modules-packages/)
   - Note that packaging is also a form of software carpentry, see further resources below
   
- Object-Oriented programming
    - Chapter in [Python Textbook](https://python-textbok.readthedocs.io/en/1.0/index.html) 
    - [Playlist](https://www.youtube.com/c/Coreyms/playlists) on Corey Schafer's YouTube channel

- [Article](https://realpython.com/python-data-structures/) about datastructures in Python

##### Software carpentry

- [Virtual Environments](https://realpython.com/python-virtual-environments-a-primer/) (`venv`, `virtualenv`, `conda`)
- Git/Version Control
     - [Pro Git Book](https://git-scm.com/book/en/v2) 
     - [Tutorial-style intro 1](https://swcarpentry.github.io/git-novice/) and [intro 2](https://education.molssi.org/python-package-best-practices/)
     - Git game  https://learngitbranching.js.org/
     - Section in [Course](https://merely-useful.tech/py-rse/)
     - [Videos](https://www.youtube.com/results?search_query=computerphile+git) on Computerphile YouTube channel
 - Documentation
      - [Article about documentation/comments](https://realpython.com/documenting-python-code/#commenting-vs-documenting-code)
      - Chapter in [Python Textbook](https://python-textbok.readthedocs.io/en/1.0/Packaging_and_Testing.html) 
	  - official style guide in [PEP8](https://www.python.org/dev/peps/pep-0008/#comments) and [PEP257](https://www.python.org/dev/peps/pep-0257/)
	  - [PEP 484](https://www.python.org/dev/peps/pep-0484/) (Type Hints)
	  - Section on [MolSSI page](https://education.molssi.org/python-package-best-practices/) 
- Python Style Guides
     - [PEP 8](https://www.python.org/dev/peps/pep-0008/) (Style guide for Python Code)
     - [PEP 257](https://www.python.org/dev/peps/pep-0257/) (Docstring Conventions)
     - [PEP 484](https://www.python.org/dev/peps/pep-0484/) (Type Hints)
     - [Google style guide](https://github.com/google/styleguide/blob/gh-pages/pyguide.md#38-comments-and-docstrings) (especially section 3.8 on comments and docstrings)
 - Testing & Test-Driven Development (TDD)
      - Background for [Testing](http://carpentries-incubator.github.io/python-testing/)
      - [Continuous Delivery YouTube Channel](https://www.youtube.com/c/ContinuousDelivery/)
      - [Blog post](https://blog.codinghorror.com/code-smells/) and [wiki article](https://en.wikipedia.org/wiki/Code_smell) about code smells, which indicate the need to refactor during TDD
      - Chapter in [Python Textbook](https://python-textbok.readthedocs.io/en/1.0/Packaging_and_Testing.html) 
      - Section on [MolSSI page](https://education.molssi.org/python-package-best-practices/) 
      - Section in [Course](https://merely-useful.tech/py-rse/)
- Packaging
     - Chapter in [Python Textbook](https://python-textbok.readthedocs.io/en/1.0/Packaging_and_Testing.html) 
     - Section on [MolSSI page](https://education.molssi.org/python-package-best-practices/) 
     - Section in [Course](https://merely-useful.tech/py-rse/)
- Profiling
     - Basic profiling in the 1st chapter of the [DataScienceHandbook](https://jakevdp.github.io/PythonDataScienceHandbook/)
     - High-Performance Python compiler [Numba](https://numba.pydata.org/)
     - [Jax](https://jax.readthedocs.io/en/latest/) (library to speed up numpy, jit-compiler, use GPUs, automatic differentiation, gradient-based optimization techniques, ..). Check out this [blog post](https://roberttlange.github.io/posts/2020/03/blog-post-10/) to get an idea what Jax can do (API might be outdated though).
- Debugging
     - [Intro/tutorial](https://www.jetbrains.com/help/pycharm/part-1-debugging-python-code.html) to PyCharm's Debugger
     - [In-depth](https://www.jetbrains.com/help/pycharm/debugging-code.html) documentation to PyCharm's Debugger
     - [Helpful explanatory video](https://www.youtube.com/watch?v=COa-JHYuW3M) by James Murphy
- [Logging](https://realpython.com/python-logging/ )

### Containerization & Docker

How to set up a local development using docker and VS Code:

- [VS Code Tutorial](https://code.visualstudio.com/docs/devcontainers/tutorial)
- [YouTube Tutorial](https://www.youtube.com/watch?v=6OxqiEeCvMI)


##### Other
- Matplotlib, Pandas & Machine Learning
     - [DataScienceHandbook](https://jakevdp.github.io/PythonDataScienceHandbook/) (with notebooks)
     - [Hands on Machine Learning Book](https://github.com/ageron/handson-ml2) (link to associated notebooks `tools_pandas.ipynb` and `tools_matplotlib.ipynb`)
     - [Playlists] on Corey Schafer's YouTube channel (pandas & matplotlib)
