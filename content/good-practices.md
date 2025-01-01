# Good practices and tools

:::{objectives}
- Know about tools that can help you **spot code problems** and help you following
  a **consistent code style** without you having to do it manually.
- Get an overview of **AI-based tools** and how they can help you
  writing code.
:::


## Follow the PEP 8 style guide

- Please browse the [PEP 8 style guide](https://pep8.org/) so that you are familiar with the most important rules.
- Using a consistent style makes your code easier to read and understand for others.
- You don't have to check and adjust your code manually. There are tools that can do this for you (see below).


## Linting and static type checking

A **linter** is a tool that analyzes source code to detect potential errors, unused
imports, unused variables, code style violations, and to improve readability.
- Popular linters:
  - [Autoflake](https://pypi.org/project/autoflake/)
  - [Flake8](https://flake8.pycqa.org/)
  - [Pyflakes](https://pypi.org/project/pyflakes/)
  - [Pycodestyle](https://pycodestyle.pycqa.org/)
  - [Pylint](https://pylint.readthedocs.io/)
  - [Ruff](https://docs.astral.sh/ruff/)

In this course we will focus on [Ruff](https://docs.astral.sh/ruff/) since it
can do **both checking and formatting** and you don't have to switch between
multiple tools.

:::{discussion} Linters and formatters can be configured to your liking
These tools typically have good defaults. But if you don't like the defaults,
you can configure what they should ignore or how they should format or not format.
:::

This code example (which we possibly recognize from the previous section about
{doc}`profiling`)
has few problems (highlighted):
```{code-block} python
---
emphasize-lines: 2, 7, 10
---
import re
import requests


def count_unique_words(file_path: str) -> int:
    unique_words = set()
    forgotten_variable = 13
    with open(file_path, "r", encoding="utf-8") as file:
        for line in file:
            words = re.findall(r"\b\w+\b", line.lower()))
            for word in words:
                unique_words.add(word)
    return len(unique_words)
```

Please try whether you can locate these problems using Ruff:
```console
$ ruff check
```

If you use version control and like to have your code checked or formatted
**before you commit the change**, you can use tools like [pre-commit](https://pre-commit.com/).


## Use an auto-formatter

[Ruff](https://docs.astral.sh/ruff/) is one of the best tools to automatically
format your code according to a consistent style.

To demonstrate how it works, let us try to auto-format a code example which is badly formatted and also difficult
to read:
:::::{tabs}
  ::::{tab} Badly formatted
  ```python
  import re
  def  count_unique_words (file_path : str)->int:
    unique_words=set()
    with open(file_path,"r",encoding="utf-8") as file:
      for line in file:
        words=re.findall(r"\b\w+\b",line.lower())
        for word in words:
          unique_words.add(word)
    return len(   unique_words   )
  ```
  ::::

  ::::{tab} Auto-formatted
  ```python
  import re


  def count_unique_words(file_path: str) -> int:
      unique_words = set()
      with open(file_path, "r", encoding="utf-8") as file:
          for line in file:
              words = re.findall(r"\b\w+\b", line.lower())
              for word in words:
                  unique_words.add(word)
      return len(unique_words)
  ```

  This was done using:
  ```console
  $ ruff format
  ```
  ::::
:::::

Other popular formatters:
  - [Black](https://black.readthedocs.io/)
  - [YAPF](https://github.com/google/yapf)

Many editors can be configured to automatically format for you when you save the file.

It is possible to automatically format your code in Jupyter notebooks!
For this to work you need
the following three dependencies installed:
```
jupyterlab-code-formatter
black
isort
```

More information and a screen-cast of how this works can be found at
<https://jupyterlab-code-formatter.readthedocs.io/>.


## Consider annotating your functions with type hints

Compare these two versions of the same function and discuss how the type hints
can help you and the Python interpreter to understand the function better:
:::::{tabs}
  ::::{tab} Without type hints
  ```{code-block} python
  ---
  emphasize-lines: 1
  ---
  def count_unique_words(file_path):
      unique_words = set()
      with open(file_path, "r", encoding="utf-8") as file:
          for line in file:
              words = re.findall(r"\b\w+\b", line.lower())
              for word in words:
                  unique_words.add(word)
      return len(unique_words)
  ```
  ::::
  ::::{tab} With type hints
  ```{code-block} python
  ---
  emphasize-lines: 1
  ---
  def count_unique_words(file_path: str) -> int:
      unique_words = set()
      with open(file_path, "r", encoding="utf-8") as file:
          for line in file:
              words = re.findall(r"\b\w+\b", line.lower())
              for word in words:
                  unique_words.add(word)
      return len(unique_words)
  ```
  ::::
:::::

A (static) type checker is a tool that checks whether the types of variables in your
code match the types that you have specified.

Popular tools:
- [Mypy](https://mypy.readthedocs.io/)
- [Pyright](https://github.com/microsoft/pyright) (Microsoft)
- [Pyre](https://pyre-check.org/) (Meta)


## Consider using AI-assisted coding

We can use AI as an assistant/apprentice:
- Code completion
- Write a test based on an implementation
- Write an implementation based on a test

Or we can use AI as a mentor:
- Explain a concept
- Improve code
- Show a different (possibly better) way of implementing the same thing


:::{figure} img/productivity/chatgpt.png
:alt: Screenshot of ChatGPT
:width: 100%

Example for using a chat-based AI tool.
:::

:::{figure} img/productivity/code-completion.gif
:alt: Screen-cast of working with GitHub Copilot
:width: 100%

Example for using AI to complete code in an editor.
:::

:::{admonition} AI tools open up a box of questions
- Legal
- Ethical
- Privacy
- Lock-in/ monopolies
- Lack of diversity
- Will we still need to learn programming?
- How will it affect learning and teaching programming?
:::


## Debugging with print statements

Print-debugging is a simple, effective, and popular way to debug your code like
this:
```python
print(f"file_path: {file_path}")
```
Or more elaborate:
```python
print(f"I am in function count_unique_words and the value of file_path is {file_path}")
```

But there can be better alternatives:

- [Logging](https://docs.python.org/3/library/logging.html) module
  ```python
  import logging

  logging.basicConfig(level=logging.DEBUG)

  logging.debug("This is a debug message")
  logging.info("This is an info message")
  ```
- [IceCream](https://github.com/gruns/icecream) offers compact helper functions for print-debugging
  ```python
  from icecream import ic

  ic(file_path)
  ```


## Often you can avoid using indices

### Iterating
:::::{tabs}
  ::::{tab} Verbose and can be brittle
  ```python
  scores = [13, 5, 2, 3, 4, 3]

  for i in range(len(scores)):
      print(scores[i])
  ```
  ::::

  ::::{tab} Better
  ```python
  scores = [13, 5, 2, 3, 4, 3]

  for score in scores:
      print(score)
  ```
  ::::
:::::


### Enumerate if you need the index
:::::{tabs}
  ::::{tab} Verbose and can be brittle
  ```python
  particle_masses = [7.0, 2.2, 1.4, 8.1, 0.9]

  for i in range(len(particle_masses)):
      print(f"Particle {i} has mass {particle_masses[i]}")
  ```
  ::::

  ::::{tab} Better
  ```python
  particle_masses = [7.0, 2.2, 1.4, 8.1, 0.9]

  for i, particle_mass in enumerate(particle_masses):
      print(f"Particle {i} has mass {particle_mass}")
  ```
  ::::
:::::



### Zip if you need to iterate over two collections

:::::{tabs}
  ::::{tab} Using an index can be brittle
  ```python
  persons = ["Alice", "Bob", "Charlie", "David", "Eve"]
  favorite_ice_creams = ["vanilla", "chocolate", "strawberry", "mint", "chocolate"]

  for i in range(len(persons)):
      print(f"{persons[i]} likes {favorite_ice_creams[i]} ice cream")
  ```
  ::::

  ::::{tab} Better
  ```python
  persons = ["Alice", "Bob", "Charlie", "David", "Eve"]
  favorite_ice_creams = ["vanilla", "chocolate", "strawberry", "mint", "chocolate"]

  for person, favorite_ice_cream in zip(persons, favorite_ice_creams):
      print(f"{person} likes {favorite_ice_cream} ice cream")
  ```
  ::::
:::::


### Unpacking
:::::{tabs}
  ::::{tab} Verbose and can be brittle
  ```python
  coordinates = (0.1, 0.2, 0.3)

  x = coordinates[0]
  y = coordinates[1]
  z = coordinates[2]
  ```
  ::::

  ::::{tab} Better
  ```python
  coordinates = (0.1, 0.2, 0.3)

  x, y, z = coordinates
  ```
  ::::
:::::


### Prefer catch-all unpacking over indexing/slicing

:::::{tabs}
  ::::{tab} Verbose and can be brittle
  ```python
  scores = [13, 5, 2, 3, 4, 3]

  sorted_scores = sorted(scores)

  smallest = sorted_scores[0]
  rest = sorted_scores[1:-1]
  largest = sorted_scores[-1]

  print(smallest, rest, largest)
  # Output: 2 [3, 3, 4, 5] 13
  ```
  ::::

  ::::{tab} Better
  ```python
  scores = [13, 5, 2, 3, 4, 3]

  sorted_scores = sorted(scores)

  smallest, *rest, largest = sorted(scores)

  print(smallest, rest, largest)
  # Output: 2 [3, 3, 4, 5] 13
  ```
  ::::
:::::


### List comprehensions, map, and filter instead of loops

:::::{tabs}
  ::::{tab} For-loop
  ```python
  string_numbers = ["1", "2", "3", "4", "5"]

  integer_numbers = []
  for element in string_numbers:
      integer_numbers.append(int(element))

  print(integer_numbers)
  # Output: [1, 2, 3, 4, 5]
  ```
  ::::

  ::::{tab} List comprehension
  ```python
  string_numbers = ["1", "2", "3", "4", "5"]

  integer_numbers = [int(element) for element in string_numbers]

  print(integer_numbers)
  # Output: [1, 2, 3, 4, 5]
  ```
  ::::

  ::::{tab} Map
  ```python
  string_numbers = ["1", "2", "3", "4", "5"]

  integer_numbers = list(map(int, string_numbers))

  print(integer_numbers)
  # Output: [1, 2, 3, 4, 5]
  ```
  ::::
:::::

:::::{tabs}
  ::::{tab} For-loop
  ```python
  def is_even(number: int) -> bool:
      return number % 2 == 0


  numbers = [1, 2, 3, 4, 5, 6]

  even_numbers = []
  for number in numbers:
      if is_even(number):
          even_numbers.append(number)

  print(even_numbers)
  # Output: [2, 4, 6]
  ```
  ::::

  ::::{tab} List comprehension
  ```python
  def is_even(number: int) -> bool:
      return number % 2 == 0


  numbers = [1, 2, 3, 4, 5, 6]

  even_numbers = [number for number in numbers if is_even(number)]

  print(even_numbers)
  # Output: [2, 4, 6]
  ```
  ::::

  ::::{tab} Filter
  ```python
  def is_even(number: int) -> bool:
      return number % 2 == 0


  numbers = [1, 2, 3, 4, 5, 6]

  even_numbers = list(filter(is_even, numbers))

  print(even_numbers)
  # Output: [2, 4, 6]
  ```
  ::::
:::::


## Know your collections

How to choose the right collection type:
- Ordered and modifiable: `list`
- Fixed and immutable: `tuple`
- Key-value pairs: `dict`
- Dictionary with default values: `defaultdict` from {py:mod}`collections`
- Members are unique: `set`
- Optimized operations at both ends: `deque` from {py:mod}`collections`
- Cyclical iteration: `cycle` from {py:mod}`itertools`
- Adding/removing elements in the middle: Create a linked list (e.g. using a dictionary)
- Priority queue: {py:mod}`heapq` library
- Search in sorted collections: {py:mod}`bisect` library

What to avoid:
- Need to add/remove/modify elements at the beginning or in the middle? Don't use a list.
- Need to make sure that elements are unique? Don't use a list.


## Making functions more ergonomic

- Less error-prone API functions and fewer backwards-incompatible changes by enforcing keyword-only arguments:
  ```python
  def send_message(*, message: str, recipient: str) -> None:
      print(f"Sending to {recipient}: {message}")
  ```

- Use dataclasses or named tuples or dictionaries instead of too many input or output arguments.

- Docstrings instead of comments:
  ```python
  def send_message(*, message: str, recipient: str) -> None:
      """
      Sends a message to a recipient.

      Parameters:
      - message (str): The content of the message.
      - recipient (str): The name of the person receiving the message.
      """
      print(f"Sending to {recipient}: {message}")
  ```

- Consider using `DeprecationWarning` from the {py:mod}`warnings` module for deprecating functions or arguments.


## Iterating

- When working with large lists or large data sets, consider using generators or iterators instead of lists.
  Discuss and compare these two:
  ```python
  even_numbers1 = [number for number in range(10000000) if number % 2 == 0]

  even_numbers2 = (number for number in range(10000000) if number % 2 == 0)
  ```

- Beware of functions which iterate over the same collection multiple times.
  With generators, you can iterate only once.

- Know about {py:mod}`itertools` which provides a lot of functions for working with iterators.


## Dataclasses

Dataclasses are often a good alternative to regular classes:
:::::{tabs}
  ::::{tab} Regular class
  ```python
  class Point:
      def __init__(self, x, y, z):
          self.x = x
          self.y = y
          self.z = z

      def __repr__(self):
          return f"Point(x={self.x}, y={self.y}, z={self.z})"

      def __eq__(self, other):
          if not isinstance(other, Point):
              return NotImplemented
          return self.x == other.x and self.y == other.y and self.z == other.z
  ```
  ::::

  ::::{tab} Dataclass
  ```python
  from dataclasses import dataclass

  @dataclass
  class Point:
      x: float
      y: float
      z: float
  ```
  ::::

  ::::{tab} Named tuple
  ```python
  from collections import namedtuple

  Point = namedtuple("Point", ["x", "y", "z"])
  ```
  ::::
:::::


## Use relative paths and pathlib

- Scripts that read data from absolute paths are not portable and typically
  break when shared with a colleague or support help desk or reused by the next
  student/PhD student/postdoc.
- {py:mod}`pathlib` is a modern and portable way to handle paths in Python.


## Project structure

:::{instructor-note}
Examples will be added.
:::

- As your project grows from a simple script, you should consider organizing
  your code into modules and packages.
- Wrap your main function in a `if __name__ == "__main__":` block.


## Reading and writing files

:::{instructor-note}
To be added.
:::


## Use subprocess instead of os.system

- Many things can go wrong when launching external processes from Python. The {py:mod}`subprocess` module is the recommended way to do this.
- `os.system` is not portable and not secure enough.


## Parallelizing

:::{instructor-note}
To be added.
:::
