# Basics

> This page will be updated as we get a better grasp of incoming members'
> Python background. For Python beginners,
> [the official Python tutorial](https://docs.python.org/3/tutorial/) is
> a very good resource to pick up the language as you work through the
> exercise in the following section.

## Running Python code
The Python interpreter can be accessed interactively in the terminal:

```bash
$ python
Python 3.12.0 (main, Jul 24 2023, 12:13:16) [Clang 14.0.0 (clang-1400.0.29.202)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello, world!")
Hello, world!
>>> 1 + 2
3
>>>
```

> Depending on your Python installation, you may need to use `python3` or `py` instead
> of the `python` command.

But, for larger scripts, it's better to write Python code into a file, which can be run
later. For example, try creating a file called `hello_world.py` with the contents:

```python
print("Hello, world!")
```

We can then run this file in the terminal:
```bash
$ python hello_world.py
Hello, world!
```

It should also possible to run the file using the "Run" button in your editor of choice.

## Simple examples
Many of the following code snippets have been taken from
[the official Python tutorial](https://docs.python.org/3/tutorial/). The purpose of this
section is to give a *very* broad overview of what Python looks like. The hope is that you
will learn the relevant parts of Python as you need them, while you work on the telemetry
pipeline as part of this training. To learn the language more thoroughly, please refer
to that tutorial.

---

Variables in Python are declared using the `[name] = [value]` syntax, and messages can
be output to the screen using the `print` function:
```python
i = 256 * 256
print("The value of i is", i) # output: The value of i is 65536
```

---

On any line, everything after a `#` is a comment. They do not affect to behavior of your code.
```python
# This is a comment.
x = 1 # This is also a comment.
# x = 2
print(x) # output: 1
```

---

Python defines a set of default types available to use in your code, including integers, floating
point (fractional) numbers, complex numbers, booleans (true/false), strings (text), lists, and much more.

```python
an_integer = -10
a_float = 1.5
a_complex_number = 1 + 2j
a_boolean = True
a_string = "This is a string!"
a_list = [1, 2, 3, 4, 5]
```

---

Python programmers can use the `if`, `elif`, and `else` keywords to cause different behavior depending
on given conditions.

```python
x = 5
if x < 0:
    x = 0
    print('Negative changed to zero')
elif x == 0:
    print('Zero')
elif x == 1:
    print('Single')
else:
    print('More') # output: More
```

---

`for` and `while` loops can be used to repeat certain behavior. Loops are commonly used
to iterate over collections, like lists. For example:

```python
a = ['Mary', 'had', 'a', 'little', 'lamb']
for i in range(len(a)):
    print(i, a[i]) # output:
                   # 0 Mary
                   # 1 had
                   # 2 a
                   # 3 little
                   # 4 lamb
```

---

Functions are blocks of code which can defined once and called many times later.
Functions are Python are defined using the `def` keyword:


```python
def fib(n): # write Fibonacci series up to n
    a, b = 0, 1
    while a < n:
        print(a, end=' ')
        a, b = b, a+b
    print()

# Now call the function we just defined:
fib(100) # output: 0 1 1 2 3 5 8 13 21 34 55 89
fib(2000) # output: 0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597
```

---

Sometimes you'll want to use code that someone else wrote or that lives in a different file
in your own project. You can do this with the `import` statement.


```python
import math

print(math.sin(0.5)) # output: 0.479425538604203
```

Here, we imported the `math` module from Python's standard library to calculate
the sine of 0.5. The standard library is automatically available to your Python
program. Other packages must be installed before they can be used.

---

These examples only scratch the surface of what there is to learn about Python. Again,
there are many good online resources that go into much more detail!
