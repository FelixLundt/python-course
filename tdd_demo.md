## General points

The key feature of Test-Driven Development (TDD) is the idea to write tests before writing a single line of code. While I hope that nobody doubts the fact that tests as such are useful, I know that simply telling you to write tests for your functions first won't cut it. Since I honestly believe TDD can help you write better code in less time (yes, both of those things), I want to make an effort to explain it to you as illustratively as possible. The main points below are taken from [this video](https://www.youtube.com/watch?v=llaUBH5oayw).

I'd like to emphasize right away that TDD is not so much about testing per se, it really is a design principle for 'good' code that has testing at its core. In that sense TDD goes beyond mere checking of input-output relations as in unit testing. The idea is to have testability in mind while writing code, which is enforced by the fact that the tests are written first. When done right, this leads to code that looks different and in many ways better than code produced in the traditional order (implement first, then check for correctness). Note that in the latter process, the test has a limited function and doesn't really impact the design.

However, what do we mean by 'good' code specifically? In short, we could come up with the following characteristics:

- It works and does something useful
- Easy to read
- Easy to test
- Easy to change
- Simple
- Efficient/fast

In the tutorial, we also discussed this from the perspective of someone doing research and what they want to do with their code. The characteristics above allow to (or at least make it easier to):

- Produce results reliably and quickly
- Share code with collaborators/publish code 
- Maintain/improve/update code when necessary with minimal effort
- Easily and intuitively use code

These points are all pretty universally useful and important. For me personally, in my day to day use of code in a scientific setting, I see it as a tool to flexibly and quickly test new ideas. All of the above points are critical for that.

Some of the attributes code needs to exhibit to achieve this are:

- Modular
- Loosely coupled
- Cohesive
- Separation of concerns
- Information hiding

Generally, those attributes can overlap substantially, they are not 5 independent properties. Together, they express the goal to design code consisting of several, independent subunits (modularity & loose coupling) which ideally have only a single function (cohesion & separation of concerns) and should be as simple as possible. This makes sure that each part is easier to read/understand. Testing those subunits is also going to be simple (because test cases are also independent). Furthermore, changing or improving the implementation in one subunit should be easy as well, since those changes shouldn't affect other parts (information hiding).

This abstract explanation is probably not too useful, but the attributes hopefully become more clear in the TDD examples below, where I discuss them using specific code. It should also become more apparent how those attributes are related to the characteristics listed above. 

The premise behind TDD is that by focusing on tests extensively, all these features are promoted in the code you write. 

## The process
With this rough idea of what constitutes good code in mind, I'll now explain the process of actually practicing TDD. To me, there are two main steps to this. 

#### Structure the problem

First, think about the problem you are trying to solve and what pieces of code you need for that. This should be done before writing any tests, and certainly before writing any code. If you don't know exactly what code you're about to write is supposed to do, you'll have a hard time figuring out how to test it in a simple way. Note that this is a first benefit of TDD! It's very easy to start implementing half-baked ideas and consequently messy design, but almost impossible to write a simple test in that case. Furthermore, this step does take experience and practice. Also, often there is no one perfect solution. It can sometimes be hard to know when the structure you came up with is good enough to start implementing. One good rule of thumb to help with that is the idea that every function/method should have only one purpose. If you have boiled the problem down that far, you should be able to start writing tests! 

The next section explains the three phases you can use to implement and design your code, based on the structure you came up with in the first step. Of course, during writing tests and code iteratively, you might make changes to this structure and improve on it.

#### Red-Green-Refactor

As I mentioned in the tutorial, in TDD you write tests *before* you begin with coding up any functionality. TDD is often implemented in an iterated manner, summarized in the mantra 'Red-Green-Refactor'. Before I explain what this means, I want to state the goal of what's to follow. We want to design code iteratively, in growing levels of complexity, by using tests to guide us. TDD is supposed to help us focus and keep it as simple as possible, to avoid writing unnecessary or unnecessarily complicated code. 

'Red-Green-Refactor' refers to the subloop within this process, that is repeated until a piece of code correctly fulfills the function we wrote it for, and does so in code that shows the attributes and characteristics outlined in the previous section. Specifically, the mantra refers to the stages of writing a test which your code does not yet pass ('Red'), of modifying your code such that it passes the test ('Green'), and of improving your program afterwards. 

In each cycle, the objective is to add one simple test for another piece of functionality ('Red'). If you can't put your test in simple terms, you should think about your structure! Tests should be easy, such that the code passing them can be easy as well. After writing the test, you should modify your code in the simplest possible way that passes the test ('Green'). Don't overthink it, this phase is not about making things pretty. Focus on the specific test at hand, and let the tests guide your implementation collectively. Lastly, have a look at your code again ('Refactor') and try to spot opportunities to improve it, while still passing all tests you've written. Note that this *includes* the code used for testing.

A small detour about refactoring: Keep an eye out for [code smells](https://blog.codinghorror.com/code-smells/) such as

- long functions
- duplicate code
- a lot of if/else code,
- switch-case demons (doesn't apply to Python though)
- logic simplifications
- design issues.

These are often good places for improvements. Furthermore, refactoring also includes removing unused code and adding comments/documentation.

I suppose all this still sounds esoteric, so I'd like to illustrate what I mean with an example, which I have taken from [this](https://www.youtube.com/watch?v=ln4WnxX-wrw&list=PLwLLcwQlnXByqD3a13UPeT4SMhc3rdZ8q&index=24) YouTube video.

## TDD Example 1: Anagram solver

Let's imagine the following scenario: Someone gives you a file containing a list of words. You are now supposed to write a program, with which they can go through this list to find anagrams to any given word (not necessarily contained in the list). The program should tell how many anagrams it found and list them.
Note: For simplicity, we won't check whether any strings we're dealing with are proper words. Furthermore, we won't care about capitalization and use only lower case letters. We are also going to ignore anagrams with spaces (e.g., 'monkey writes' would be an anagram of 'New York Times'). Lastly, we'll assume that there are no duplicates in the list.

### Bad example

Let's start with a piece of code inside a Python script `bad_anagrams.py` that does just this:
```python

from pathlib import Path  
  
DICT = '/usr/share/dict/web2'  
  
  
def show_anagrams(word):  
    words = Path(DICT).read_text().splitlines()  
    anagrams = []  
    for anagram in words:  
        if is_anagram(word, anagram):  
            anagrams.append(anagram)  
  
    print(f"There are {str(len(anagrams))} anagrams " f"of '{word}' in the dictionary")  
    for anagram in anagrams:  
        print(anagram)  
  
  
def is_anagram(anagram, word):  
    if len(anagram) != len(word):  
        return False  
	for letter in anagram:  
        if letter in word:  
            word = word.replace(letter, '', 1)  
        else:  
            return False  
return True
```

This script would then be used by importing the `show_anagrams` function elsewhere, and calling it with any given word we might want to check as a string.

Take a moment to read through the code to see how it works. In the following, I want to discuss weaknesses in the design, not so much in the implementation details. So while the two functions `show_anagrams` and `is_anagram` could definitely be improved in terms of efficiency, this is not where the focus is going to be. (If you were to make those changes, it should be clear which code smells apply here.)

So how should we evaluate how good a solution this is? Well, it is correct and does what it should, in a reasonably efficient manner. Furthermore, one might think it is a simple solution, since it basically follows the instructions step by step. 

However, first of all it is impossible to test this code, mainly because `show_anagrams` does not return anything. You can test the `is_anagram` function, but that only tells you that your code is able to check whether two words are anagrams of one another. So there is no way to know whether you access the word list correctly and that the printed message is correct! In this simple example, it might be possible to convince yourself by inspection that the code works, but this might no longer be the case in more complicated settings. And correctness means nothing without tests to prove it! 

Second, I'd argue the code is not actually simple. It doesn't reflect the structure of the problem very well, and mixes accessing the word list, doing the anagram business, and printing the message. You have to keep the entire problem setting with all its details in mind to understand the code. In other words, it's pieces are not designed to do only one particular thing, so they are actually more complicated than necessary.

So, correctness and simplicity are not the code's strong suits. Furthermore, it could be more readable, for the same reasons why it isn't as simple as it could be. Likewise, it certainly isn't easy to change (to different input lists or different output messages), because both of these things are hard-coded into the script and the `show_anagrams` function. Therefore, making a change at one point makes further changes elsewhere necessary.

Having established the weaknesses of the solution above, let's try to view them through the lens of our five hallmarks of good code: modularity, cohesion, separation of concerns, information hiding, loose coupling.

The code is certainly not modular. Everything is in one place, and there are (almost) no subparts that could be reused.

The code also doesn't display separation of concerns. It fulfills three different functions (reading from a file, looking for anagrams, printing a message), but these are not implemented in distinct parts of the code.

Similarly, it is also not cohesive (cohesion meaning that elements doing related things are close to each other), since unrelated subtasks are contained in a single function.

For the same reason, the different parts (if you can call them that) in this bad example are tightly coupled and heavily depend on each other.

Finally, the code also doesn't hide any information or abstract the problem, it is pretty brute-force and everything depends on little implementation details. The `is_anagram` function is a small exception (because it hides the details about what an anagram is from the rest of the code and abstracts the property of two words being anagrams into one function checking this property), but that alone isn't enough.

As mentioned before, the five hallmarks are often overlapping, and they certainly do so here. I hope this example made it clear that they refer to slightly different aspects, though, which are very useful to keep in mind when you're looking at code.

Before we move on to a better example designed using TDD, it is instructive to think about ways to make the bad example more testable.
First, in order to check for correctness of the printed message, one would have to intercept writing to the console. This amounts to nothing else than decoupling the printing from the rest of the code. This is exactly the point though: we would improve the design and make the code more testable in the process.
Furthermore, you would have to replace the hard-wired word list with something under your control. This way, you could make sure that what you're feeding your anagram solve is actually what you want. This would decouple reading from a file from the code taking care of the anagrams. Again, increasing testability also improves the code.
In the example below, TDD is going to lead us to a solution in which both of these things are achieved.

###  Good Example with TDD

#### Think about the problem
Before we write our first test, let's briefly discuss what we are trying to do. There are really three things we are concerned about:

1. We need to access a list of words, stored somewhere on disc.
2. Given a new word, we need to find anagrams to it in the word list.
3. We have to display the results somehow.
It's worth pointing out that separating these three aligns directly with the two improvements to the bad examples we suggested at the end of the previous section.

Let's start with implementing the second point. Note that the following is somewhat specific to using `pytest`.


#### Implement the anagram solving part

##### 1. step: Write a failing test (Red)

Before we can really start with writing a test, let us first lay out the 'project structure':
```
 |-> anagrams.py 
 |-> tests_anagrams.py
 \-> main.py

```
We'll put the code for the anagram solver in `anagrams.py`, the corresponding tests in `tests_anagram.py`, and using the anagram solver will take place in `main.py`. 

In TDD, it's crucial to keep things as simple as possible at all times. We want to make progress incrementally, and the steps should be small. This will mean that we're circling through the Red, Green, Refactor phases a lot of times, which gives us a lot of opportunities to think about design and implementation. This makes it easy to find errors/bugs and will provide a lot of feedback for ourselves.

One very simple test is to check whether a word is recognized as an anagram to itself. A possible way to do this looks like that:
```python

def test_should_find_test_word_as_self_anagram():  
    assert list_anagrams(["word"], "word") == ["word"]
```
For clarity, let me unpack this. There are a few important remarks to be made:

- In TDD, the standard is to have **one assertion per test**, i.e. writing one test function for each piece of functionality. This has a quite a [few advantages](https://stackoverflow.com/questions/1962358/tdd-why-is-there-only-one-test-per-function):

    - Close to TDD idea of testing/implementing one behavior at a time.
    - Separating tests keeps them independent, such that a failing test tells you exactly where the problem is. Otherwise failed assertions executed later could just fail because of earlier problems. This also leads to better test coverage in the presence of failures, and even if there are failing tests, the others are still actually testing your code.
    - If you change your code to behave differently, it's easy to figure out which tests you need to change accordingly.
    
    In general, I'd recommend one module in your `tests` package per function. I'm not aware of a generally agreed-upon standard though, and as long as it doesn't get out of hand, I'll except one module in the `tests` package per module in your code base.

- **Naming:** You need to start the name of the test with `test` for `pytest` to be able to recognize it. I like to start the rest with the word `should`, because it creates a bias to specify the behavior I want the code to exhibit (which is exactly what the test is for at this point). Furthermore, be sure to make the name expressive and specific to what you want to test.

    Furthermore, it can help to introduce variables for the arguments and return value of the function to clearly express what is going on:

    ```python

    def test_should_find_test_word_as_self_anagram():  
        test_word = "word"
        reference_list = [test_word]
        found_anagrams = list_anagrams(reference_list, test_word)
        assert test_word in found_anagrams
    ```

- Overall, **tests should be simple** and not require too much setup or teardown code. If it does, treat this as a hint that your design might be suboptimal. 
- The first test might cover a very simple case, but it has an enormously **important function**: Most of the design of the interface to the function under test is done here already! In this case, we decided that our function is supposed to be taking in a list of words (as the dictionary), and a testword, which we would like to find anagrams for. It's going to return something that will contain the anagrams it found (i.e., most likely a list). 
- Related to the last point: It would also be fine to check the output explicitly by using the assertion
    ```python
	assert list_anagrams(["testword"], "testword") == ["testword"] 
	```
	Arguably, that would even be the better test. But remember that TDD is about design first. It's okay if the tests are not perfect, we really want to use them to guide our design. If the tests lead you to incrementally improve things while you go along: Great! Here, the fact that the test only check for membership should be a hint that we should probably use a set instead of a list. However, these decisions don't have to be made at this early stage. However, it is important to keep in mind that at some point, we'll have to check the output explicitly to make sure we actually get what we want.
	
I'd like to stress that even after writing this first simple test, the design choice makes the code better than the bad example immediately! Because the code we're testing only does one thing, i.e., finding anagrams and not reading in some file first or printing a message, it is going to be more modular and more loosely coupled, with improved separation of concerns.

Of course, in principle there's no reason why you wouldn't come up with this design without using TDD. But I hope you can appreciate that by starting with the test, you are forced to think about your implementation in a very specific way right from the start. 

The only thing missing to having a failing test is to make sure it is actually failing. There's no point to running it right now, since we would simply get an import error. Having a failing test always means getting a proper assertion error from the test. It's even better if you can predict how exactly the assertion fails, because that shows you understand exactly what your implementation is doing.

In order to get an assertion error, let's add the minimum amount of code to `anagrams.py`:
```python
def find_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:	
    return []

```
Try running the test yourself now, it should give you an assertion error. Parts of the message `pytest` displays might be a little confusing though. I think that's because it's tailored for assertions for equality (using the `==` operator), when it interprets the left hand side of the equality as the result from the function call, and the right hand side as the expected result.


##### 2. step: Make the test pass (Green)

In our first Green phase, our task is to write just enough code to pass the test. We shouldn't try to be too smart, this is not the time to worry about how good our solution is. This step might seem silly at first, but it serves a very important purpose: By writing the simplest code possible to pass the test, we actually test whether the test works! And due to the simplicity of the code, we can be as sure as possible that we know exactly what is going on.

I'd suggest the following implementation of the `list_anagrams` function for now:
```python
def list_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:
		return [test_word]
```

Running the test should pass now. This can serve as confirmation that both our test and the implementation work. This is not trivial! It's more common than you'd think to write a test that doesn't test anything. By going through both the Red and Green phases, we made sure that the test actually does fail if things go wrong, but also that it does pass if the code does fulfill the requirement. By keeping the implementation as simple as possible, we ensure that this is not an accident. Implementation design is then done during the refactor phase, where we're free to make changes to both implementation and test to improve our code. This is much safer to do **after** having established a proper test.


##### 3. step: Improve implementation and test (Refactor)
In this case, both test and implementation are so simple that we don't need to refactor just yet.


##### 4. step: Another simple test

For the sake of brevity, we'll speed up a little and group together Red-Green-Refactor cycles. However, I think it would be a good exercise to stop here and take a moment to think for yourselves about what we should test next.

Although there isn't a unique best way to proceed, I'd argue that it would be best to fix a hole in our first test. Up until now, we're just returning the `"testword"` in a list, but we didn't include the case that this word is not even contained in `word_list`. So let's add a test for that (to `test_anagrams.py`):
```python
def test_should_reject_test_word_if_not_in_reference_word_list():
	assert "testword" not in list_anagrams(["words"], "testword")

```
Running the test reveals that it fails as expected. 

One very simple change to the code in `anagrams.py` would be to check explicitly if the word is contained in `reference_word_list`:
```python
def list_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:
	if word in reference_word_list:
		return [word]
	return []
```
Running **both** tests now should give you two passing tests. It's obviously crucial to run all previous tests to make sure our changes didn't mess with what we had solved before.

For the Refactor-phase I'd like to point towards the list of code smells: There's a conditional and multiple return statements, which is something we could clean up. The code is still pretty simple though, it would also be fine to leave it like this for now. But I'm a proponent of cleaning up while I go along, so I'd suggest this:
```python
def list_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:
	words = []
	if word in reference_word_list:
		words.append(word)
	return words
```



##### 5. step: Adding anagram functionality

So far, we only dealt with the simple case of entire words being equal, but of course we haven't really dealt with finding anagrams. The simplest way to test this looks something like this:
```python
def test_should_list_simple_anagram():
    assert "wordtest" in list_anagrams(["wordtest"], "testword")
```
Running this test now fails, because the returned list is empty.

To pass the test, we don't need to do much. The easiest is this:
```python
def list_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:
	words = ["wordtest"]
	if ref_word in reference_word_list:
		words.append(ref_word)
	return words
```
Again, that's not a sophisticated solution by any stretch of the imagination. But resist the urge to do more than necessary before you have a passing test while being absolutely certain that your code-test combination works! Running the test will reveal that everything works as expected.

Moving on to the Refactor-phase, it's obvious what we have to do: Get rid of the hard-coded insertion of the anagram from the test case. If someone were pedantic, strictly speaking this isn't an improvement on the current design, it's adding new functionality. And we want to do the latter driven by our tests, so let's leave our code where it is right now and write a new test instead! I'd also argue that we are limited by the simplicity of our test cases at the moment. Tweaking and polishing the implementation now might be premature, since we can be pretty certain that we have to make substantial changes anyway. However, you could also make a compelling argument to improve the implementation here already - again, there's no one right way.

In order to force our code to be more flexible, let's make the test case more complicated, change the words in it and address the fact that we haven't rejected words which aren't anagrams yet:
```python
def test_should_list_anagram_and_reject_non_anagram():  
    assert ("seat" not in list_anagrams(["seat", "sett"], "test")  
            and "sett" in list_anagrams(["seat", "sett"], "test")
		    )
```
This of course fails, but not because the non-anagram is rejected, but because the anagram is not found.

To pass this test, we have to do some work. Since we can't rely on the testword `"test"` being in the list, we have to replace checking for this with actually checking if two words are anagrams. And we have to do this for each word in the `word_list`. So I'd propose:
```python
def find_anagrams(reference_word_list: list[str], test_word: str) -> list[str]:	
	anagrams = []
	for ref_word in reference_word_list:
		if is_anagram(ref_word, test_word):
			anagrams.append(ref_word)
	return anagrams


def is_anagram(word, test_word):  
    if len(word) != len(test_word):  
        return False  
    for letter in word:  
        if letter in test_word:  
            test_word = test_word.replace(letter, '', 1)  
        else:  
            return False  
    return True
```
Overall, this is actually enough to find anagrams! The implementation in `_is_anagram` is the same as in the bad example above. It isn't super pretty, but we'll leave it at that for the sake of brevity. Importantly, this will make comparing the solutions even more striking. We'll deem this solution sufficient for our purposes for now.

However, I'd suggest refactoring that last test:
```python
def test_should_list_anagram_and_reject_non_anagram():  
    anagrams_found = list_anagrams(["seat", "sett"], "test", lambda x: x)  
    assert ("seat" not in anagrams_found and "sett" in anagrams_found)
```

Side note: It would be a natural question to ask how one should get to an implementation of `_is_anagram`, and how it should be tested. My thoughts on this are the following:
- The effect of this function on the overall design are negligible. It does a very straightforward thing, and any changes within the function won't impact code outside it. Therefore, the benefits of writing tests beforehand are rather small. If you have a clear idea of how you can implement the function, I think it's fine to just go ahead. If you don't, TDD could be a helpful tool nevertheless.
- Despite TDD not being super relevant for this function, I don't see a reason to not test it briefly. Two cases are enough, both for words of equal length: One with an actual anagram word pair, and one where the pair aren't anagrams. That's basically all the cases there are! 


##### 6. step: Make sure the implementation is actually correct

At this point it's not too difficult to see that our solution finds all anagrams. However, sometimes that might not be so easy, so that you might want to make sure a particular case is covered already. Or you might even think there are cases your solution doesn't cover yet, write a test for that, and subsequently find out you're all set. 

Generally, I'd recommend to not include unnecessary tests if you're certain you don't need them. Try to keep your testing suite light! However, your tests also serve as a low-level documentation of your code, which tells readers exactly how it behaves. So if you think that some test isn't strictly necessary, but serves an important illustrative purpose, it's okay to keep it in. You don't necessarily have to go as far as to delete tests you already wrote. However, sometimes you can restructure the other tests and kick out one of the others instead.

Furthermore, it's possible to end up with an implementation that solves things you haven't written a test for yet. Thus, there's always a danger that once you change the implementation, the tests don't actually guarantee correctness any longer. It's worth trying to think about such cases beforehand, although it's often hard to cover all of them.

##### Conclusions

I'd like to summarize a little before moving on. First, it's worth pointing out that our first tests had nothing to do with anagrams, but they forced us to think about the structure of the problem and the design of the interface. In the end, we just had to replace a simple check for some property and add a loop, while we were able to build on the structure we set up before.

Second, I hope it became clear how TDD separates interface design (in Red phase) from implementation design (Green phase, but mainly Refactor phase). As mentioned, keeping the steps small offers protection and allows for quick changes without too many repercussions if something goes wrong. 


####  Implement printing out the message

##### Write the test
This part might be confusing. The reason for this is that we're getting closer towards the edge of our system, where we want to print out our results to the console. While testing the internals of our code is easy to do (if designed properly), printing something sends it off elsewhere, beyond our control.

This is a typical situation in which testing (and thus TDD) becomes more difficult. The usual way to solve this is to design everything in a controllable way as far as possible, and make the interface to anything outside our code as clean and well-behaved as possible. 

The idea is the following: In our tests, we'll try to replace the `print` function with another function we can control (later called `mock_display`, such that we can check how this function is called ([mocks]](https://en.wikipedia.org/wiki/Mock_object) in testing)/[mocking](https://stackoverflow.com/questions/2665812/what-is-mocking) are a far-reaching concept. That way we can be confident that in a real use case, we can simply _inject_ the `print` function in place of the `mock_display` function, and the behavior will be just as we want it. 

Let's be more specific and add a test to check our ability to display the results of our anagram finder:
```python
def test_should_display_results():
    test_word = 'cab'
    found_anagrams = ['abc', 'bac']

    displayed_messages = []

    def mock_display(message):
        displayed_messages.append(f'sent: {message}')

    display_results(test_word, found_anagrams, mock_display)
    
    message_total_num_anagrams = "sent: There are 2 anagrams of 'cab' in the dictionary"
    message_anagram_abc = "sent: abc"
    message_anagram_bac = "sent: bac"

    assert displayed_messages == [message_total_num_anagrams,
                                  message_anagram_abc,
                                  message_anagram_bac]
```
Let's unpack this test, starting with the line
```python
display_results(test_word, found_anagrams, mock_display)
```
Again, for the function that will be responsible to display the results, we made a design choice here. 
In this test case, what 'displaying' means is determined by what the function `mock_display` does: It appends slightly modified (by adding `'sent: '` at the beginning) messages to the list `displayed_messages`, and this is supposed to be happening by calling `display_results` (no other line actually executes something in this test). 

Checking whether the messages that were generated in this way are correct then amounts to explicitly checking the contents of `displayed_messages` after calling `list_anagrams`. What they should be is determined in the variables `message_total_num_anagrams`, `message_anagram_abc` and `message_anagram_bac`.

If you are confused at this point, don't worry. It took me a while to figure out how this works. One key realization for me was that at this stage, you should forget about implementation. This test is really only determining what your code is supposed to do! And by writing this test, you make sure you have enough control to check whether it actually does what it should.

For completeness, there are more elegant and flexible ways to use mocking. Check out [this link](https://docs.python.org/3/library/unittest.mock.html) if you're interested. For this example, I thought it would be least confusing to define the `mock_display` function explicitly, so I didn't want to get into that.

Before I forget: this test obviously fails.

##### Implementation

Here's the final solution that I'd put forward for `anagrams.py`:
```python
def list_anagrams(word_list, test_word):  
    anagrams = []  
    for word in word_list:  
        if is_anagram(word, test_word):  
            anagrams.append(word)  
    return anagrams  
  
  
def is_anagram(anagram, word):  
    if len(anagram) != len(word):  
        return False  
    for letter in anagram:  
        if letter in word:  
            word = word.replace(letter, '', 1)  
        else:  
            return False  
    return True  
  
def display_results(test_word, found_anagrams, display_function):
    display_function(f"There are {str(len(found_anagrams))} anagrams of '{test_word}' in the dictionary")  
    for anagram in found_anagrams:  
        display_function(anagram)
```
The changes to before are:
- new `display_results` function, which is going to be responsible for printing (`print` will be provided as third argument to `list_anagrams`)
- in `list_anagrams`, there's a call of `_display_results` after the loop over `word_list`

This now passes the last test we added, but the previous ones don't work anymore, because we changed the signature of `list_anagrams` by adding a third parameter. We thus have to call it with one in the previous tests, and the easiest option would be to pass a function that doesn't do anything. 

One final form for the test module `test_anagrams.py` would be:
```python
from anagrams import list_anagrams  
  
  
def test_should_list_same_word_as_anagram():  
    assert "testword" in list_anagrams(["testword"], "testword", lambda x: x)  
  
  
def test_should_list_same_word_as_anagram():  
    assert "testword" not in list_anagrams(["wordtest"], "testword", lambda x: x)  
  
  
def test_should_list_simple_anagram():  
    assert "wordtest" in list_anagrams(["wordtest"], "testword", lambda x: x)  
  
  
def test_should_list_anagram_and_reject_non_anagram():  
    anagrams_found = list_anagrams(["seat", "sett"], "test", lambda x: x)  
    assert ("seat" not in anagrams_found and "sett" in anagrams_found)  
  

def test_should_display_results():  
    displayed_messages = []  
  
    def mock_display(message):  
        displayed_messages.append(f'sent: {message}')  
  
    list_anagrams(["abc", "bac", "bcd"], "cba", mock_display)  
  
    message_total_num_anagrams = "sent: There are 2 anagrams of 'cba' in the dictionary"  
    message_anagram_abc = "sent: abc"  
    message_anagram_bac = "sent: bac"  
    assert (message_total_num_anagrams in displayed_messages  
            and message_anagram_abc in displayed_messages  
            and message_anagram_bac in displayed_messages)
```

`lambda x: x` is just an inline definition of a function which returns its input. Equivalently we could define a function `foo` inside the test module and call `list_anagrams` in our test functions in this way:
```python
def foo():
	pass

def test_should_list_same_word_as_anagram():  
    assert "testword" in list_anagrams(["testword"], "testword", foo)  
```
(Same for all other tests, except the last one.) The solution with the `lambda` statement just avoids one definition outside the tests, which is generally recommended (but not super relevant for this course). If you're up for it, you can look into [fixtures](https://docs.pytest.org/en/6.2.x/fixture.html).


#### Putting everything together

All that is left for us to do is to set up `main.py`, which should then do exactly what the bad example from above does. Here's one way to do it:
```python

import urllib.request  
from anagrams import list_anagrams  
  
url = "https://www.mit.edu/~ecprice/wordlist.10000"  
with urllib.request.urlopen(url) as page:  
    page_content_bytes = page.read()  
    DICT = bytes.decode(page_content_bytes)  
    word_list = DICT.splitlines()  
  
if __name__ == '__main__':  
    while True:  
        usr_input = input('Please enter the word '  
 'for which you would like to find anagrams: ')  
        found_anagrams = list_anagrams(word_list, str(usr_input))
        display_results(usr_input, found_anagrams)
```

The block below the imports extracts a word list from a dictionary of 10000 words from some webpage (such that you can try the code yourself). Inside the `if __name__ == '__main__': ` block, I've added a prompt where you can enter test words, and below the `list_anagrams` function is used to tell you if it found any anagrams!

This code is clearly better than the bad example from above, because:

- it's more modular (print as display function, extraction of word list, anagram solver as specialized function, vs. entire, interdependent script to solve the problem)
- better abstraction/information hiding (anagram solver only cares about word list, not about a file/webpage containing the dictionary, and it isn't coupled to printing to the console either.)
- Thus also more loosely coupled
- It's super easy to adapt this code!

Note: Further improvements are absolutely possible!

- Replacing returning a list by returning a set
- More abstraction, clearer naming
- Completely separate the parts into their own modules/functions -> better cohesion & separation of concerns
   Example: displaying the results has the specific string in it, right with the anagram solver itself. Extracting that would improve the code and increase flexibility.
- Some refactoring/logic improvements in `list_anagrams` are missing

The point here is: you can stop wherever you want, depending on your needs! But if you want more abstraction/more functionality, you can let tests drive that, and the changes will be relatively easy to incorporate. One example of that would be to write a test that makes sure the anagram solver doesn't care about the specific language of the display, for example. 



### Another Example of TDD: FizzBuzz

If you're interested, check out this two-part tutorial on developing a solution to the well-known coding kata 'FizzBuzz' with TDD ([Part 1](https://www.youtube.com/watch?v=yfP_v6qCdcs) and [Part 2](https://www.youtube.com/watch?v=S4tMJKupvHg)).

