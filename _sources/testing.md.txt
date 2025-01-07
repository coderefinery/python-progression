# Automated testing

:::{objectives}
- Know **where to start** in your own project.
- Know what possibilities and techniques are available in the Python world.
:::


## Motivation

Testing is a way to check that the code does what it is expected to.

- **Less scary to change code**: tests will tell you whether something broke.
- **Easier for new people** to join.
- Easier for somebody to **revive an old code**.
- **End-to-end test**: run the whole code and compare result to a reference.
- **Unit tests**: test one unit (function or module). Can guide towards better
  structured code: complicated code is more difficult to test.


## How testing is often taught

```python
def add(a, b):
    return a + b


def test_add():
    assert add(1, 2) == 3
```

How this feels:
:::{figure} img/owl.png
:alt: Instruction on how to draw an owl
:width: 50%
:class: with-border

[Citation needed]
:::


## Where to start

**Do I even need testing?**:
- A simple script or notebook probably does not need an automated test.

**If you have nothing yet**:
- Start with an end-to-end test.
- Describe in words how *you* check whether the code still works.
- Translate the words into a script (any language).
- Run the script automatically on every code change (GitHub Actions or GitLab CI).

**If you want to start with unit-testing**:
- You want to rewrite a function? Start adding a unit test right there first.
- You spend few days chasing a bug? Once you fix it, add a test to make sure it does not come back.


## Pytest

Here is a simple example of a test:
```{code-block} python
---
emphasize-lines: 10-14
---
def fahrenheit_to_celsius(temp_f):
    """Converts temperature in Fahrenheit
    to Celsius.
    """
    temp_c = (temp_f - 32.0) * (5.0/9.0)
    return temp_c


# this is the test function
def test_fahrenheit_to_celsius():
    temp_c = fahrenheit_to_celsius(temp_f=100.0)
    expected_result = 37.777777
    # assert raises an error if the condition is not met
    assert abs(temp_c - expected_result) < 1.0e-6
```

To run the test(s):
```console
$ pytest example.py
```

Explanation: `pytest` will look for functions starting with `test_` in files
and directories given as arguments. It will run them and report the results.

Good practice to add unit tests:
- Add the test function and run it.
- Break the function on purpose and run the test.
- Does the test fail as expected?


## What else is possible

- Run the test set **automatically** on every code change:
  - [GitHub Actions](https://github.com/features/actions)
  - [GitLab CI](https://docs.gitlab.com/ee/ci/)

- The testing above used **example-based** testing.

- **Test coverage**: how much of the code is traversed by tests?
  - Python: [pytest-cov](https://pytest-cov.readthedocs.io/)
  - Result can be deployed to services like [Codecov](https://about.codecov.io/) or [Coveralls](https://coveralls.io/).

- **Property-based** testing: generates arbitrary data matching your specification and checks that your guarantee still holds in that case.
  - Python: [hypothesis](https://hypothesis.readthedocs.io/)

- **Snapshot-based** testing: makes it easier to generate snapshots for regression tests.
  - Python: [syrupy](https://syrupy-project.github.io/syrupy/)

- **Mutation testing**: tests pass -> change a line of code (make a mutant) -> test again and check whether all mutants get "killed".
  - Python: [mutmut](https://mutmut.readthedocs.io/)


## Further reading

- [CodeRefinery lesson about automated testing](https://coderefinery.github.io/testing/)
