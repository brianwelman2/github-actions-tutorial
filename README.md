# GitHub Actions Tutorial
**Table of contents**
- [GitHub Actions Tutorial](#github-actions-tutorial)
- [1. Introduction](#1-introduction)
  - [**IMPORTANT STEP!**](#important-step)
- [2. Unit Tests in Python](#2-unit-tests-in-python)
  - [2.1 Unit Test Framework](#21-unit-test-framework)
  - [2.2 Unit Tests](#22-unit-tests)
  - [2.3 Automating Unit Tests](#23-automating-unit-tests)
  - [2.4 Using Pytest](#24-using-pytest)
  - [2.5 Getting to grips with unit testing and Pytest](#25-getting-to-grips-with-unit-testing-and-pytest)
- [3. Continuous Integration](#3-continuous-integration)
  - [3.1 What is Continuous Integration?](#31-what-is-continuous-integration)
  - [3.2 CI Platforms](#32-ci-platforms)
  - [3.3 Types of CI platforms](#33-types-of-ci-platforms)
  - [3.4 GitHub-Actions Structure](#34-github-actions-structure)
  - [3.5 Python Environment Setup](#35-python-environment-setup)
  - [3.6 Using and testing kal-cal](#36-using-and-testing-kal-cal)
  - [3.7 GitHub-Actions Setup](#37-github-actions-setup)
  - [3.8 Your first job](#38-your-first-job)
  - [3.9 GitHub-Actions via GitHub.com](#39-github-actions-via-githubcom)
  - [3.10 Pros and Cons](#310-pros-and-cons)
  - [3.11 More on GitHub-Actions](#311-more-on-github-actions)
  - [3.12 Contact me for help](#312-contact-me-for-help)

# 1. Introduction
This tutorial serves as a quick introduction into concepts like *tests* and *continuous integration* to enable you to utilize GitHub's very own CI environment, *GitHub Actions*. The following software requirements are needed:

* [`casalite`](https://casa.nrao.edu/casa_obtaining.shtml)
* `python3` where `version >= 3.6`
* Python Virtual Environment (`venv` is fine)

For a catch-up on `git` commands, there is a short guide called`git-cheat-sheet.pdf` in the repo.

## **IMPORTANT STEP!**

Please ensure you do this otherwise it will not work for you. First you need to **fork** this repository so that you can edit and change it yourself. Once you have forked the repository, run the following commands:

1. Clone the repository to your machine, using `https` or `ssh`: 
   
    ```bash
    $ git clone https://github.com/brianwelman2/github-actions-tutorial.git
    ```

2. Navigate into `github-actions-tutorial/`:

    ```bash
    $ cd github-actions-tutorial/
    ```

3. Create a new branch for you, replacing `<your-name-here>` for your name. This will be **YOUR** branch to work with and make changes:

    ```bash
    $ git branch <your-name-here>
    ```

4. Move to your new branch labelled as above:

    ```bash
    $ git checkout <your-name-here>
    ```

5. Push this branch to the repo for GitHub to acknowledge it. There should be no need for a pull-request since the branch should be identical to `main`.

    ```bash
    $ git push -u origin <your-name-here>
    ```

It should all be good to go and the tutorial should be customized for work to be on this branch. Before we start, below is unit test theory to cover for the tutorial. If you understand it, you can skip to [Section 3. Continuous Integration](#3-continuous-integration).


# 2. Unit Tests in Python
## 2.1 Unit Test Framework 
This is a necessity for the longevity of any software project. As the number of lines of code grow, the ability for you to account for errors and possible bugs diminishes, because we are only human. However, we need to spot these issues one way or another before it causes any fatal software meltdown, patch them and re-release the program. This may sound like a daunting task, sinc it is intractable to all spot problems with our code we might not even know exists? Fortunately, it can easily be done with the use of a *Unit Test Framework*. But before we move to this, we need to start with the concept of *Unit Tests*.

**What is a unit test framework and how are they used?**: 
>"Simply stated, they are software tools to support writing and running unit tests, including a foundation on which to build tests and the functionality to execute the tests and report their results.They are not solely tools for testing; they can also be used as development tools on apar with preprocessors and debuggers. Unit test frameworks can contribute to almost every stage of software development, including software architecture and design, code implementation and debugging, performance optimization, and quality assurance."
>
> **\- pg. 5, Paul Hamill. 2004. *Unit test frameworks* (First. ed.). O'Reilly.**

---

## 2.2 Unit Tests
A *unit test* is a single case or scenario we wish to check for possibe bugs or errors in our code. For each issue that can be realised, we can create a unit test for it. So, lets begin with an example issue. Look at the function `create_profile` below:
```python
def create_profile(firstname, lastname, age, email, location):
    """ Create a dictionary with information about 
    a given person."""

    profile = {
        "firstname" : firstname,
        "lastname"  : lastname,
        "age"       : age,
        "email"     : email,
        "location"  : location
    }

    return profile
```
It creates a dictionary containing information about a person and returns it. Now, call it with some random string inputs (Here I am referencing [Malfurion](https://wow.gamepedia.com/Malfurion_Stormrage) from the [Warcraft Universe](https://wow.gamepedia.com/Wowpedia)):
```python 
profile = create_profile(
            firstname="Malfurion", 
            lastname="Stormrage", 
            age="15032", 
            email="druids.ftw@gmail.com", 
            location="Suramar, Broken Isles")
```
Then print our new `profile` dictionary:
```python 
print(profile)
> {'firstname': 'Malfurion', 'lastname': 'Stormrage', 'age': '15032', 'email': 'druids.ftw@gmail.com', 'location': 'Suramar, Broken Isles'}

```
and *hey presto*! It works just fine. We can try another example:
```python 
profile = create_profile(
            firstname=__import__("this"), 
            lastname=hex(0xDEADC0DE), 
            age="https://theuselessweb.com/", 
            email=10**1000,
            location=None)
```
which has the print output of:
```python 
print(profile)
>The Zen of Python, by Tim Peters

    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!
    {'firstname': <module 'this' from '/usr/lib/python3.6/this.py'>, 'lastname':    '0xDEADC0DE', 'age': 'https://theuselessweb.com/', 'email':    1000000000000000000000000000000000000000000000000000000000000000000000000000000    0000000000000000000000000000000000000000000000000000000000000000000000000000000 0000000000000000000000000000000000000000000000000000000000000000000000000000000  0000000000000000000000000000000000000000000000000000000000000000000000000000000   0000000000000000000000000000000000000000000000000000000000000000000000000000000    0000000000000000000000000000000000000000000000000000000000000000000000000000000 0000000000000000000000000000000000000000000000000000000000000000000000000000000  0000000000000000000000000000000000000000000000000000000000000000000000000000000   0000000000000000000000000000000000000000000000000000000000000000000000000000000    0000000000000000000000000000000000000000000000000000000000000000000000000000000 0000000000000000000000000000000000000000000000000000000000000000000000000000000  0000000000000000000000000000000000000000000000000000000000000000000000000000000   00000000000000000000000000000000000000000000000000000, 'location': None}
```
which is also perfectly fine obviously, since it *ran*. However, this was not the intended purpose of `create_profile` when desiging it and nor were we expecting inputs like that. But it is still a possibility that these unsavoury events can occur, so we have to somehow check for bugs like this to fix any holes in our code! This is where *unit tests* come in. 

First lets begin with the defintion of our function:
`"""Create a dictionary with information about a given person."""`

So it takes various information about the person as inputs. Lets list them and write what we expect from each input:

Argument | Data Type | Conditions
--- | --- | --- 
`firstname`, `lastname` | `string` | Contains only letters characters and some punctuation
`age` | `integer` | Greater than 0 (i.e. positive)
`email` | `string` | Formatted with an @ sign and domain, e.g. .com
`location` | `string` | A list of locations separated by commas

With this clear baseline, we can develop some simple tests to check that this function meets these conditions. One way we can do this is to run the function with a couple of input cases we know *should* and *shouldn't* be valid and see if the function continues with or rejects the inputs, respectively. For this example, I will only show the test case for the `firstname`:
```python 
def test_firstname():
    """Unit test for input `firstname` of `create_profile`. Prints test outcome
    for each input."""

    # Passed tests counter
    counter = 0

    # List of correct inputs
    correct_inputs = ["Brian", "Cyril", "Thatcher", "Kel'Thuzad"]

    # Test correct inputs
    for INPUT in correct_inputs:
        # If INPUT is correct, we continue
        try:
            create_profile(
                firstname=INPUT,        # The input we are testing
                lastname="lastname",    # Fill others with known correct values
                age=50,
                email="email@domain.com",
                location="Street, City, Country"
            )

            # Test passed for input
            print(f"PASSED for valid `firstname={INPUT}` in `create_profile`")
            counter += 1
        except:
            # Test failed for input
            print(f"FAILED for valid `firstname={INPUT}` in `create_profile`")

    # List of incorrect inputs
    incorrect_inputs = [1234, "1234", "&.&.&.&", __import__("numpy")]

    # Test incorrect inputs
    for INPUT in incorrect_inputs:
        # If INPUT is incorrect, we continue
        try:
            create_profile(
                firstname=INPUT,        # The input we are testing
                lastname="lastname",    # Fill others with known correct values
                age=50,
                email="email@domain.com",
                location="Street, City, Country"
            )
            
            # Test failed for input
            print(f"FAILED for invalid `firstname={INPUT}` in `create_profile`")
        except:
            # Test passed for input
            print(f"PASSED for invalid `firstname={INPUT}` in `create_profile`")
            counter += 1

    # Output total passed tests
    print(f"TOTAL PASSED: {counter}/8 ({round(counter/8.0*100, 2)}%)")
```
The first half of he function tests valid inputs, whilst the second half tests for invalid inputs. The tests centre around if the function raises an error or not. To run this test, call `test_firstname` in the same script, and analyze the output:
```bash
PASSED for valid `firstname=Brian` in `create_profile`
PASSED for valid `firstname=Cyril` in `create_profile`
PASSED for valid `firstname=Thatcher` in `create_profile`
PASSED for valid `firstname=Kel'Thuzad` in `create_profile`
FAILED for invalid `firstname=1234` in `create_profile`
FAILED for invalid `firstname=1234` in `create_profile`
FAILED for invalid `firstname=&.&.&.&` in `create_profile`
FAILED for invalid `firstname=<module 'numpy' from '/home/brian/.local/lib/python3.6/site-packages/numpy/__init__.py'>` in `create_profile`
TOTAL PASSED: 4/8 (50.0%)
```
Great! We can clearly see our function works for valid inputs, but completely fails on invalid inputs. From here, make the necessary changes to ensure the code is how we intended it to be and that all tests pass! 

---

## 2.3 Automating Unit Tests
The above code example would totally work, but in large programs, this is not feasible for the amount of things we would need to test for. Luckily, this is where unit testing frameworks come in. They automate the boring stuff and leave us to only write the necessary tests.

In `python`, these frameworks come in the form of packages. The native one that comes with `python` is `unittest` (see [link](https://docs.python.org/3/library/unittest.html)). However, there are plenty of external unit testing libraries out there ([list](https://blog.testproject.io/2020/10/27/top-python-testing-frameworks/)), but for this tutorial, I will use `pytest`. It is my personal preference for testing for the following reasons:

* Tests are simply designed
* Can run in parallel with `xdist` plugin
* Lots of guides, documentation and technical support
* Super easy to parameterize test inputs

Once we have setup our testing framework, all we need to do is call it whenever we make a change to our code, and the framework will systematically go through all your tests to see if any error has occured and if something has broken because of your change.

This back-and-forth between writing code, then testing, then fixing any errors that come from it, is the basic idea behind *Test-driven Software Development*. We start with an expectation of what our program should do, we create tests to make sure these requirements are met, then we write the code that satisfies these tests, and in doing so, creates an error-free and well-developed software. 

In our specific case in radio-interferometry, we predominantly work with `python` as our coding platform and so in the next section, I will demonstrate simply how to achieve the above in `python`, utilizing `pytest`.

---

## 2.4 Using Pytest
Looking at our previous example, lets develop a unit test with `pytest` for the `age` input. We begin by importing the package:
```python 
import pytest
```
And thats it. When we do this, `pytest` will handle the rest. Now we just need to make a test. Make sure the function that is doing the test has the prefix `test_` so that `pytest` can identify it and run it. As a start, write a simple test that always passes:
```python 
def test_age():
    """Unit test for input `age` of `create_profile`."""

    assert True
``` 
Here I've utilized the keyword function `assert`, which is a built-in function that takes a boolean (true or false) expression. If the input is `True`, do *nothing*. Else if the input is `False`, then raise an `AssertionError`. It is simple, but powerful in terms of testing. Tests written with `pytest` predominantly would use `assert` to see if a test has passed or failed. The above code runs `assert True` which will *always* pass, no matter what. To see this, run the command:
```bash
pytest -v <filepath>
```

where `<filepath>` is the path to your script with the test in it. Again, my script is called `script.py`, so *my* `pytest` output is:
```bash
=============================================================================================== test session starts ===============================================================================================
platform linux -- Python 3.6.9, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /usr/bin/python3
cachedir: .pytest_cache
benchmark: 3.2.3 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/brian/Code/github-actions-tutorial
plugins: benchmark-3.2.3, forked-1.3.0, xdist-2.2.1
collected 1 item                                                                                                                                                                                                  

script.py::test_age PASSED                                                                                                                                                                                  [100%]

================================================================================================ 1 passed in 0.02s ================================================================================================
```
The `-v` option lists each test as it runs. As we can see, our test was found and it passed. Time to develop the test further. A `pytest` test function will fail if an error is thrown, so we can write a simple test as follows:
```python 
def test_age():
    """Unit test for input `age` of `create_profile`."""

    # List of correct inputs
    correct_inputs = [1, 42, 1001, 2021]

    # Test correct inputs
    for INPUT in correct_inputs:
        # Pytest will fail if function raises an error       
        create_profile(
            firstname="firstname", 
            lastname="lastname",
            age=INPUT,
            email="email@domain.com",
            location="Street, City, Country"
        )

    # List of incorrect inputs
    incorrect_inputs = [-1, "hello", str, __import__("numpy")]

    # Test incorrect inputs
    for INPUT in incorrect_inputs:
        # Pytest will fail if function doesn't raise an error
        with pytest.raises(Exception) as e:
            create_profile(
                firstname="firstname",
                lastname="lastname",
                age=INPUT,
                email="email@domain.com",
                location="Street, City, Country"
            )
```
This adaptation does not use `assert` function, since we are checking for errors raised. Noting the context manager function `pytest.raises`: the test will pass if an error *is* raised, and fail if *nothing* happens. Using the `pytest` command as before, we see that indeed our test fails:
```bash
=============================================================================================== test session starts ===============================================================================================
platform linux -- Python 3.6.9, pytest-6.2.2, py-1.10.0, pluggy-0.13.1
benchmark: 3.2.3 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/brian/Code/github-actions-tutorial
plugins: benchmark-3.2.3, forked-1.3.0, xdist-2.2.1
collected 1 item                                                                                                                                                                                                  

script.py F                                                                                                                                                                                                 [100%]

==================================================================================================== FAILURES =====================================================================================================
____________________________________________________________________________________________________ test_age _____________________________________________________________________________________________________

    def test_age():
        """Unit test for input `age` of `create_profile`."""
    
        # List of correct inputs
        correct_inputs = [1, 42, 1001, 2021]
    
        # Test correct inputs
        for INPUT in correct_inputs:
            # Pytest will fail if function raises an error
            create_profile(
                firstname="firstname",
                lastname="lastname",
                age=INPUT,
                email="email@domain.com",
                location="Street, City, Country"
            )
    
        # List of incorrect inputs
        incorrect_inputs = [-1, "hello", str, __import__("numpy")]
    
        # Test incorrect inputs
        for INPUT in incorrect_inputs:
            # Pytest will fail if function doesn't raise an error
            with pytest.raises(Exception) as e:
                create_profile(
                    firstname="firstname",
                    lastname="Lastname",
                    age=INPUT,
                    email="email@domain.com",
>                   location="Street, City, Country"
                )
E               Failed: DID NOT RAISE <class 'Exception'>

script.py:48: Failed
============================================================================================= short test summary info =============================================================================================
FAILED script.py::test_age - Failed: DID NOT RAISE <class 'Exception'>
================================================================================================ 1 failed in 0.24s ================================================================================================
```
Browsing over the `pytest`-output, we see it failed because the function did not raise an error for the invalid inputs. I prefer to use `assert` functions, so here is an approach to `test_age` that does uses them:
```python 
def test_age(args):
    """Unit test for input `age` of `create_profile`."""

    # INPUT is the value, EXPECTED is the expected result of the test using INPUT
    INPUT, EXPECTED = args

    # Initial OUTCOME of the test
    OUTCOME = False

    try:
        # Run the function with INPUT
        create_profile(*INPUT)

        # Function finished with no error
        OUTCOME = True

    except:
        # Function failed with an error
        pass

    # Now test if our OUTCOME matches the EXPECTED outcome
    assert OUTCOME == EXPECTED    
```
> "The `*` in `*INPUT` is an unpacking operator. It allows grouped values in a `tuple` or `list` to be distributed, in order, over the function arguments."

This is quite different from the previous and won't work straight away. The most notabe reason for this is that there are no inputs yet. There is only an argument called `args` and we use it to get variables `INPUT` and `EXPECTED`. To provide inputs, we are going to use a `python` wrapper called `pytest.fixture`. 

`pytest.fixture` is placed on functions we want `pytest` to evaluate before it begins testing. The most common use for this is creating session-wide inputs for all tests. An example of this for our case would be the following (to apply a wrapper, we use the `@` symbol):
```python 
@pytest.fixture
def args():
    """Provide `INPUT` and `EXPECTED` for `test_age`"""

    # Inputs for `create_profile`
    INPUT = ("firstname", "lastname", 42, 
                "email@domain.com", "Street, City, Country")
    
    # Expected output
    EXPECTED = True

    # Return arguments
    return INPUT, EXPECTED
```
Now when we begin testing, this function is executed and returned to all tests that call it as an argument. Place this code above the function `test_age` and now we can run `pytest`, again, using the same command as before. The test should now pass as per values in `args`:
```bash
=============================================================================================== test session starts ===============================================================================================
platform linux -- Python 3.6.9, pytest-6.2.2, py-1.10.0, pluggy-0.13.1
benchmark: 3.2.3 (defaults: timer=time.perf_counter disable_gc=False min_rounds=5 min_time=0.000005 max_time=1.0 calibration_precision=10 warmup=False warmup_iterations=100000)
rootdir: /home/brian/Code/github-actions-tutorial
plugins: benchmark-3.2.3, forked-1.3.0, xdist-2.2.1
collected 1 item                                                                                                                                                                                                  

script.py .                                                                                                                                                                                                 [100%]

================================================================================================ 1 passed in 0.02s ================================================================================================
```
We can take this one step further and parametrize it! I will create two lists of ages just below my imports: *correct ages* and *incorrect ages*. Each element is a `tuple` that consists of the `INPUT` and the corresponding `EXPECTED` result based on if `INPUT` is valid or invalid.
```python 
# Integers 0 to 999
CORRECT_AGES = [(i, True) for i in range(10**3)]

# Integers -1 to -999
INCORRECT_AGES = [(-i, False) for i in range(1, 10**3)]

# Concatenate inputs
AGES = CORRECT_AGES + INCORRECT_AGES
```
Next we add an argument to `pytest.fixture` and change `args` as follows:
```python 
@pytest.fixture(params=AGES)
def args(request):
    """Provide `INPUT` and `EXPECTED` for `test_age`"""

    # Unpack param
    AGE, EXPECTED = request.param

    # Inputs for `create_profile`
    INPUT = ("firstname", "lastname", AGE, 
                "email@domain.com", "Street, City, Country")

    # Return arguments
    return INPUT, EXPECTED
```
The `params` argument will indicate to `pytest` that we need to parametrize, and to access each value, we call `request.param`. Now each value in `AGES` will create an individual test, listing each as it progresses. This allows for more insightful testing and for combinations of inputs as well. After running `pytest` again, the last few lines of the output will be:
```bash
...
FAILED script.py::test_age[args1993] - assert True == False
FAILED script.py::test_age[args1994] - assert True == False
FAILED script.py::test_age[args1995] - assert True == False
FAILED script.py::test_age[args1996] - assert True == False
FAILED script.py::test_age[args1997] - assert True == False
FAILED script.py::test_age[args1998] - assert True == False
======================================================================================== 999 failed, 1000 passed in 8.89s =========================================================================================
```
Which is what we expected:

1. Integers 0 to 999 (1000 cases) should pass the test
2. Integers -1 to -999 (999 cases) should fail the test

This is excellent! Now, if we wish to add more inputs to test, we just need to append it to `AGES`. This concludes the basic usage of `pytest` for this tutorial.

---

## 2.5 Getting to grips with unit testing and Pytest
I have done a very simple and crude guide on unit testing in `python` for the needs of this tutorial, and so do not take my test designs as standard, since every project has its own requirements and restrictions. Look through the documentation and figure out what works for you! If you want to learn more, here is a list of resources and guides to help explain these concepts in an in-depth manner:

* [Unit Tests in Python || Python Tutorial || Learn Python Programming, *by Socratica* (YouTube)](https://www.youtube.com/watch?v=1Lfv5tUGsn8)
* [Python Testing Cookbook, *by Greg Turnquist* (Textbook)](https://edu.heibai.org/Python_Testing_Cookbook.pdf)
* [Testing python applications with pytest, *by SEMAPHORE* (Article)](https://semaphoreci.com/community/tutorials/testing-python-applications-with-pytest)
* [Python Testing with pytest, *by Brian Okken (2017)* (Textbook)](http://library.sadjad.ac.ir/opac/temp/18467.pdf)
* [pytestguide.readthedocs.io (Website)](https://pytestguide.readthedocs.io/en/latest/)

--- 

# 3. Continuous Integration
## 3.1 What is Continuous Integration?
*Continuous Integration* (or *CI* for short) is a process used by modern-day software developers to simulatenously develop a single project, making merges of changes, testing the source code and reviewing components of the project. Normally, CI is coupled with another process called *Continuous Delivery*, which is concerned with releasing of the software for public use, but for our circumstances, this is not entirely necessary (unless you plan to pursue software developing in astronomy).

Returning to the former process, I will explain briefly why it is necessary in our environment. If you have a look at [kern-suite](https://kernsuite.info/), there are a lot of software packages. Most of the packages are developed by unique individuals with specific coding styles. Some packages could be written in `python`, `C` or even `C++`. Going deeper, each language has multiple different versions and with each version, there are sometimes nuances to keywords and defintions. Other times there are complete paradigm shifts, like the `print` keyword going from a *statement* in `python2` to a *function* in `python3`, causing the annoying change-over from *no-brackets* to *brackets* that breaks all our old programs.

I could continue for quite a while listing all the infinite variations between these packages, but the pressing question I have is, when they release the software, how do they make sure it works on the computers of the people who will use it?

>"CI is essentially a software engineering task where source code is both merged and tested on a mainline trunk. A CI task can do any multitude of tasks, including testing software components and deploying software components. The act of CI is essentially prescriptive and is an act that can be performed by any developer, system administrator, or operations personnel. Continuous integration is continuous because a developer can be continuously integrating software components while developing software."
>
> **\- Jean-Marcel Belmont. 2018. *Hands-on continuous integration and delivery: build and release quality software at scale with Jenkins, Travis CI, and CircleCI*. Packt.**

At first, this sounds like quite an impossible problem to solve. We would have to somehow run the software on multiple machines with different operating systems and variations in programming versions. The good news is, the impossible can be made possible with CI platforms.

---

## 3.2 CI Platforms
The name is quite self-explanatory, i.e. it is a platform that hosts CI-related services. Such services include *version-control management*, *building software*, *communications hub*, *task automation* and *testing* to name a few. For the purposes of this tutorial, I will be looking at *task automation* and the *testing* aspect.

Peering back at our previous example, most CI platforms come with the ability to *spawn* machines, with custom settings and perform tasks on said machines. There are a number of ways to do this, namely through *virtual machines* or *containers* (i.e. [Docker](https://www.docker.com/)). All the CI platform needs is minimum requirements for these different machines, and it does the rest automatically! This brings us to our two checks we would like to perform:

1. Does the software install and run?
2. Does it pass all software tests?

Once we give it these instructions, it will run them on each machine it spins up. Once complete, it will report back to us the success of the operations. Since most CI platforms are hosted online, this entire process happens in the background, so we can continue working while it finishes. When we receive the report, it will precisely show where our software breaks and on which machine, so we can apply the needed fixes to make sure its compatible with that machine. 

The final and most important aspect to this whole process is that is independent of the user and their machine. This creates a generalized and unbiased approach to installing and running your software so that it has the best chance to run on all systems.

---

## 3.3 Types of CI platforms 
   
With usage in mind, we can now create our own CI process. There are quite a few examples of CI platforms to choose from:

1. [Jenkins](https://www.jenkins.io/)
2. [CircleCI](https://circleci.com/)
3. [TeamCity](https://www.jetbrains.com/teamcity/)
4. [Travis-CI](https://travis-ci.com/)
5. [GitHub-Actions](https://github.com/features/actions)

For this tutorial, I will be using *GitHub Actions*, for the following reasons:

* Comes setup already with your GitHub repository
* Simple to get started
* Configuration file is easy to handle
* Allows for integration with Docker
* Can outsource execution to your own machines (i.e. run through Rhodes Clusters)

---

## 3.4 GitHub-Actions Structure
The structure is quite simplistic and easy to understand. It begins with *workflows* which is a single instance of the CI running. Within a workflow, there can be multiple *jobs* that are run for different purposes. Each job has a specific goal in mind, so for example, we wish to test our software on `Ubuntu` vs `Fedora` operating systems. There will be a job to test the software with `Ubuntu` and another for `Fedora`, separately and labelled likewise. 

In each job, we define *steps* it has to take to setup the environment, install any additional softwares and then run the tests (or any other command). Note, GitHub runs these workflows based on triggers. This highly customisable, but by default, we normally trigger a workflow on a `git push` or `git pull_request`.

All this information will be stored in a configuration file for GitHub to read and run, and that is it. GitHub will take of the rest and report the results. Before I move to an applied example, we first need to create our testing environment.

---

## 3.5 Python Environment Setup

To begin, we need to make a virtual environment with Python. Any virtual environment package will work, but for simplicity, I will use `venv`. In the `github-actions` directiory:
```bash
$ python -m venv venv
```
which creates a new python environment in a directory called `venv`. To activate it:
```bash
$ cd venv/
$ source bin/activate
```
A prefix of `(venv)` should appear in the command-line of your terminal. All references to `python` will now be in `venv`. To deactivate the environment:
```bash
$ deactivate
```
Lastly, with the *activated environment*, upgrade `pip` (you might not have to do this depending on which virtual-environment package you used):
```bash
$ pip install --upgrade pip
```
Python version for now should not matter for the needs of this task. Next, I'm going to install my very own Python package I have been working on for my masters: [kal-cal](https://github.com/brianwelman2/kal-cal). 

> **kal-cal** is a Python library developed to provide proof-of-concept tools for Kalman Filtering and Smoothing Theory (see Bayesian Filtering and Smoothing by Simo S ̈arkk ̈a) as a replacement calibration framework for Radio-Interoferometric Gains Calibration (see Non-linear Kalman Filters for calibration in radio interferometry by Cyril Tasse). This library is part of the master's thesis work of Brian Welman (@brianWelman2 on github) through Radio Astronomy Techniques and Technologies under SARAO during the period of 2020 to 2021.

To install this package, use the following command:
```bash
$ pip install https://github.com/brianwelman2/kal-cal/archive/refs/heads/main.zip
```
It has quite a lot of dependencies so please be patient. To test if it has installed, open a Python-terminal with:
```bash
$ python
```
And import kal-cal:
```python
> import kalcal
```
---

## 3.6 Using and testing kal-cal

Navigate to the folder `main`:
```bash
$ cd main/
```
> **NB**: Ensure [`casalite`](https://casa.nrao.edu/casa_obtaining.shtml) is installed for this next part. 

Next, run the `kalcal_script.py`:
```bash
$ python kalcal_script.py
```
Which will do the following:

1. Create an empty measurement set using `simms`
2. Create jones matrices with gains-only data and store it in `normal.npy`
3. Generate model-visibilities and visibilities with noise
4. Load all the above data
5. Create input parameters for Extended Kalman Filter and Smoother
6. Run the *Extended Kalman Filter*, using `numba`
7. Run the *Extended Kalman Smoother* (x3), using `numba`
8. Plot *gains-magnitude* over time, i.e. ![formula](https://render.githubusercontent.com/render/math?math=g_pg_q^\dagger) against true gains
10. Done

The resulting plot should be as follows:

![kal-cal ouput](output.png)

Continuing, I wish to test some features of kal-cal. Using `pytest` as our testing framework, open the file `main/test_example.py`, which will have two functions:

1. `test_check_kalcal_import`
    > Sometimes its is important to see if you can actually just import the modules and sub-elements of your python program, so we can write a test to check for this.

2. `test_jones_correct_dimensions`
    > Data-values are always essential to test, but dimensions are also important to check, especially with lots of linear algebra based operations, where dimension is key.

Starting with the first, an example test we could use is as follows:
```python
def test_check_kalcal_import():
    """Test if you can import kalcal."""

    # Boolean for test outcome
    PASSED = False

    try:
        # Run the import for kalcal
        import kalcal

        # If it made it here, it passed
        PASSED = True

    except ImportError:
        # Import failed, so test failed
        pass

    # Run assert
    assert PASSED
```
This simple layout runs the test by seeing if an `ImportError` is raised or not. In the terminal (in `main/`), run:
```bash
$ pytest -v test_example.py
```
If kal-cal is installed properly, the test should pass with the following output:
```bash
=============================================================================================== test session starts ===============================================================================================
platform linux -- Python 3.6.9, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /home/brian/Code/github-actions-tutorial/venv/bin/python
cachedir: .pytest_cache
rootdir: /home/brian/Code/github-actions-tutorial/main
collected 2 items                                                                                                                                                                                                 

test_example.py::test_check_kalcal_import PASSED                                                                                                                                                            [ 50%]
test_example.py::test_jones_correct_dimensions PASSED                                                                                                                                                       [100%]

================================================================================================ 2 passed in 0.13s ================================================================================================
```
For the second stub, I can check dimensions by using the `shape` attribute and brute-force the needed outcome. I've done the first part already by loading in the jones data-file using `numpy`:
```python
def test_jones_correct_dimensions():
    """Test if dimensions of jones is correct."""

    # Open datafile and extract jones
    with open("normal.npy", "rb") as data:
        jones = np.load(data)

    # Correct Dimensions
    n_time = 200
    n_ant = 7
    n_chan = 1
    n_dir = 1
    n_corr = 2

    # Correct shape
    correct_shape = (n_time, n_ant, n_chan, n_dir, n_corr)

    # Get jones shape
    jones_shape = jones.shape

    # Assert shapes are the same
    assert correct_shape == jones_shape
```
Run the same pytest command as before to get:
```bash
=============================================================================================== test session starts ===============================================================================================
platform linux -- Python 3.6.9, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /home/brian/Code/github-actions-tutorial/venv/bin/python
cachedir: .pytest_cache
rootdir: /home/brian/Code/github-actions-tutorial/main
collected 2 items                                                                                                                                                                                                 

test_example.py::test_check_kalcal_import PASSED                                                                                                                                                            [ 50%]
test_example.py::test_jones_correct_dimensions PASSED                                                                                                                                                       [100%]

================================================================================================ 2 passed in 0.13s ================================================================================================
```
Now we have two tests that run on our machines that pass. For a larger test example, in the repo, I have a folder called `tests` which is my current testing-suite for kal-cal. Navigate to the root-directory of the repo (`github-actions-tutorial/`), and run the following command:
```bash
$ pytest tests/
```
Which systamically goes through each test-script testing various parts of kal-cal. The tests should take about a minute or so. Before we automate this, we need to setup of GitHub-Actions

---

## 3.7 GitHub-Actions Setup

This process is actually quite simple for our means. All GitHub requires is a configuration file with all the instructions to create a Python Environment and what commands to run. 

Make a directory, in the root directory of the repo, called `.github/`. In `.github`, make another directory called `workflows/` and move into it:
```bash
$ mkdir .github/
$ cd .github/
$ mkdir workflows/
$ cd workflows/
```
This is where GitHub looks for the workflow configuration files to run. Create a `yaml` file called:
```
<your-name-here>_kalcal.yml
```
where `<your-name-here>` is the name of your branch.

>**NB**: From this point onwards, where ever you see the word `brian`, replace it with your branch name you created earlier.

Open the file in an editor. The first thing we need is to give it a name and its triggers. Below, I will set the trigger to be for a `git push` command:
```yaml
name: brian-kalcal

on:
    push:
        branches: ["brian"]
```

>The `on` keyword lists the triggers for this workflow, and additionally, we specifically asking it to look for when there is a `push` command on the `brian` branch specificially, and no other branch.

Add and commit the workflow file, then push it to GitHub:
```bash
$ git add brian_kalcal.yml
$ git commit -m "Workflow file added"
$ git push origin brian
```
>From this point onwards, GitHub will automatically run the workflow file and will run it each time a `push` command is done.

If you go to [`github-actions-tutorial`](https://github.com/brianwelman2/github-actions-tutorial) on GitHub, switch to your branch, and click the `Actions` tab, all information for GitHub-Actions will be listed, including the result of your new workflow triggered by your workflow file. It failed since there were no jobs to run just yet.

---

## 3.8 Your first job

Now that the link has been made between our repo and GitHub-Actions, we can make our first job. In `brian_kalcal.yml`, below the previous code, I will create a job. We start with the `jobs` keyword and the name of our first job, testing the kal-cal tests:

```yaml
jobs:

  # First Job
  kalcal-tests:
    name: Tests for kal-cal package
    runs-on: ubuntu-18.04
    strategy:
      matrix:
        python-version: [3.6]
```
> Each job requires certain keywords to operate. In this scenario, we list:
> 
> 1. `name` - Identifier for our job.
> 
> 2. `runs-on` - Operating System to spawn for the job
> 
> 3. `strategy` - Builds configuration matrix for your job
> 
> 4. `matrix` - The configuration matrix itself
> 
> 5. `python-version` - Configuration that lists all the python-versions to run this job on 
>
> The last 3 keywords essentially will create sub-jobs from this job, where each sub-job will have its own version of python as listed by 5.

This sets up the machine for the job, now we need to setup the software and give it steps. We can do this by writing the `steps` keyword below and at the same indentation-level as our new job, `kalcal-tests`. Our first step will be to create the Python-environment:

```yaml
steps:
    # Create Python Environment
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
```
> Our step is given a `name` that describes what it going to do, after which we instruct it to use the predefined GitHub-Action's function `actions/setup-python@v2` to install `Python` on the machine, but `with` a specific version in mind. The `uses` keyword is used to select an action function in GitHub-Actions list of actions. The `${{ matrix.python-version }}` expression retrieves the python-version for associated with that specific sub-job. 

For our next steps, we need to copy the GitHub repo, update `pip` and install kal-cal on the machine:
```yaml
    # Make a copy of your repo
    - name: Checkout code
      uses: actions/checkout@v2

    # Upgrade pip 
    - name: Upgrade pip 
      run: |
        python -m pip install --upgrade pip

    # Install kal-cal
    - name: Install kal-cal via pip
      run: |
        pip install https://github.com/brianwelman2/kal-cal/archive/refs/heads/main.zip
```
> Again, the `uses` keyword is used, but here it calles `checkout@v2` which copies everything in the GitHub repo to the current folder in the machine. The `run` keyword stipulates a terminal command for the machine to run. Here, it is running `python` and `pip`. At this point in time, our CI is setup to do anything, and just requires testing.

Finally, we add the step for testing with `pytest`:
```yaml
    # Run the tests
    - name: Test with pytest
      run: |
            pytest tests/
```
> The workflow will begin testing and will display a fail if ANY test fails, else it will display a pass.

The full file should look as follows:

```yaml
name: brian-kalcal

on:
    push:
        branches: ["brian"]

jobs:

  # First Job
  kalcal-tests:
    name: Tests for kal-cal package
    runs-on: ubuntu-18.04
    strategy:
      matrix:
        python-version: [3.6]

    steps:
    # Create Python Environment
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}

    # Make a copy of your repo
    - name: Checkout code
      uses: actions/checkout@v2

    # Upgrade pip 
    - name: Upgrade pip 
      run: |
        python -m pip install --upgrade pip

    # Install kal-cal
    - name: Install kal-cal via pip
      run: |
        pip install https://github.com/brianwelman2/kal-cal/archive/refs/heads/main.zip

    # Run the tests
    - name: Test with pytest
      run: |
            pytest tests/
```

To get this running, add the workflow file and push it to GitHub under your branch. To see it running, go to [`Actions Tab`](https://github.com/brianwelman2/github-actions-tutorial/actions/). 

Your will see a list of the last commits of each push command. A yellow dot should appear the one you recently just pushed. Click on the commit, which will give a layout of information of all the jobs in this workflow. On the left, all the jobs in the workflow and their statuses are listed. You can click on a specific job to see information on the steps and watch it run.

If the test fails, it will list which step it failed on so you can go back and make necessary corrections and changes. Otherwise, everything is setup to automatically test your code whenever you push to GitHub. To add more tests, just add them to `tests/`. If you want to create more jobs, just list it below the `kalcal-tests` job at the same indentation-level.

This script is also setup to test multiple python versions. Find the `strategy` keyword and in the `python-version` array, list other versions like `3.7`, `3.8` and `3.9`. Push the changes and now GitHub-Actions will spawn sub-jobs for each python-version! 

For an example of how I test kal-cal, see [kal-cal/actions](https://github.com/brianwelman2/kal-cal/actions) where all my workflow history can be found. 

---

## 3.9 GitHub-Actions via GitHub.com

For those who don't like manually setting this up, GitHub offers an assisted setup on the repo website to create a workflow and jobs. Navigate back to the [`Actions Tab`](https://github.com/brianwelman2/github-actions-tutorial/actions/), and a `New workflow button` should be displayed. 

Clicking it takes you to a page that lists a bunch of templates you can follow to setup any workflow, for any particular reason, and for any programming language (not just Python). Pick one that suits you and follow the instructions.

---

## 3.10 Pros and Cons
GitHub-Actions has the following perks:

* Notifications can be setup on the repo so that GitHub to contact you when a workflow has finished and what the result was. Possible methods of communication are discord, slack, email, sms, tweets, calendar and telegram to name a few.

* Automates the whole testing process and ensures good quality developing.

* Customize triggers to only run a workflow when a certain words found in the last commit.

* Test documentation for your repo and your code.

The only limitation I have encountered with GitHub-Actions is that because it is free, the processing power and memory facilities are tiny. The specifications are:

* 2-core CPU
* 7 GB of RAM memory
* 14 GB of SSD disk space
But this can be overcome with self-hosting (see next section).

---

## 3.11 More on GitHub-Actions

This tutorial barely scratches the surface what the platform can do. Below is a list of additional resources and guides on how to setup GitHub-Actions for various needs:

* Documentation for GitHub-Actions: 
  * https://docs.github.com/en/actions

* Setup GitHub-Actions using *Docker*: 
  * https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action

* Marketplace for Custom Actions for your workflow:
  * https://github.com/marketplace?type=actions

* Run GitHub-Actions on *Self-Hosted* machines (run on Rhodes Clusters):
  * https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners

* GitHub-Actions Interactive Guides:
  * https://docs.github.com/en/actions/guides

---

## 3.12 Contact me for help

For anymore help with this or topics relating to it, feel free to contact me via email at brianallisterwelman@gmail.com, on GitHub under `brianWelman2` or on slack [RATT/RARG-#ratt-students](https://ratt-rarg.slack.com/archives/C01R85AR78U).
