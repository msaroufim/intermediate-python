
# Python features you may not know
Copying code snippets I liked from Python Distilled by David Beazley
### print redirect to file

You can redirect the output of a print statement to a file

```python
print("hello world", out=file)
```

## magic functions for classes
When creating a new kind of datastructure you can override `__repr__` and `__len__` to support native `print()` and `len()` for your data structures respectively.

This even works if you'd like to support operator overloading with `__add__`, `__sub__` and `__mul__`

## Replace try, finally statements with context manager

```python
# Don't do this
try:
    ...
finally:
    resource.release()

# Do this
with resource:
    ...
```

## Modules are files

```python
# hello.py
def hello_world:
    print("hello")

# from main.py
if __name__ == "__main__":
    from hello import hello_world
    hello_world()
```

## Packages are a group of files
Packages in python are nothing but a directory which looks like

```python
package_name/
    __init__.py
    package_name.py
    util.py
    test.py
```

You call specific functions by navigating directories
`from package_name.util import util_function`

## Which package am I using 
If you're confused about which package you're using you can run

```python
import torch
torch.__file__
```

Which will show you the exact directory where `torch` is installed on your disk, if you're trying to debug some issues you can come in that file change a few things, add print or breakpoint statements and you should be good to go

## Debugging

If you find yourself adding print statements everywhere it's probably a good idea to instead add `breakpoint()` which will help you step through or into code and inspect variable values.

## Define your own Exception

```python
class NetworkError(Exception):
    def __init__(self, errno, msg):
        self.args = (errno, msg)
        self.errno = errno
        self.errmsg = msg
```


## Reference counting

You can get reference count on objects, when it reaches 0 the object gets garbage collected

```python
import sys
sys.getrefcount(var)
```

Most of the time you don't need to think about garbage collecting

## Everything is first class

You can make functions, modules, exceptions as elements of a dictionary or enum which can help you refactor your code easily

## Protocols
Python defines protocols like Object protocol, Number, Comparison, Conversion, Container, Iterator, Attribute , Function, Context manager that each require you to support some magic functions. You can figure out what those magic functions very easily by taking a look at `from rich import inspect`

## What is something

Instead of using print statements we suggestt you try out

```python
from rich import inspect
a = 2
inspect(a)
inspect(a, methods=True)
inspect(a, all=True)
```

## You can test docstrings

```python
def add(a, b):
    """
    Add two numbers a and b. For example
    >>> add(2,3)
    5
    >>>
    """
```

If `add()` ever changes the doc will be incorrect so can run tests using `doctest .`

## Asynchronous functions can't be run by themselves

```python
async def greeting(name):
    print(f'Hello {name}')

import asyncio
asyncio.run(greeting('Guido'))
```

## Static methods don't need self

```python
class Account:
    @staticmethod
    def get_bank_name():
        return "Chase"
    
    @property
    def owner(self)"
        return self._owner
    
    @owner.setter
    def owner(self,value):
        # add some validation on what owner can be 
```

## Multiple dispatch

```python
if isinstance(obj, Duck):
    handle_duck()
elif isinstance(obj, Trombonist):
    handle_trombonist()
```

Instead can create handlers

```python
handlers = {
    Duck : handle_duck,
    Trombonist : handle_trombonist,
    Cyclist : handle_cyclist
}

# Dispatch
def dispatch(obj):
    func = handlers.get(type(obj))
    if func:
        return func(obj)
    else:
        raise RuntimeError(f"No handler for {obj}")
```

## Class definition process
Can add conditional statements class `__init__()` or other functions

Can dynamically create a class 

```python
methods = {
    '__init__' : __init__,
    'deposit' : deposit,
    'withdraw' : withdraw,
}

Account = types.new_class('Account', (), exec_body = lambda ns: ns.update(methods))
```

Classes are all created by a meta class

## Deploying a package
1. Create a setup.py in root directory with package name, dependencies ec.
2. Run python setup.py sdist
3. Get a wheel file that you then upload with twine upload

An alternative is to use `poetry add, poetry build, poetry publish`