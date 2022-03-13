# Typed Python

Copying code snipepts from Robust Python by Patrick Viafore

Definitely unit tests and uses types whenever you can, if default types aren't enough you can create your own

```python
from enum import Enum
class Device(Enum):
    CPU = torch.device("cpu")
    GPU = torch.device("gpu")
```

It's important to note that in Python Objects are first class citizens which means that you can have arbitrary code or classes or functions or lambdas or errors or exceptions as elements of an enum which can help generalize your code.

So let's say you wanted to build a multiple dispatch system in Python, you can do it with an enum (Take that Julia but jk)

```python
class Dispatcher(Enum):
    int = int_adder
    float = float_adder
    long = long_adder

# You want to create a mechanism to add numbers where you're going to call some optimized kernels

def int_adder(a,b):
    c = call_cool_cuda_thing(a,b)
    return c

def float_adder(a,b):
    c = call_c++_function(a, b)

def general_adder(a : Numeric, b : Numeric):
    type_of_a = type(a)
    c = Dispatcher.type_of_a(a,b)
    return c

class Numeric(Enum):
    float
    int
    long 
```

You can then use this type to create more complex types using dataclasses

```python
from dataclasses import dataclass

@dataclass
class Context:
    name : str,
    code : int,
    device : Device,
    data : Union[str, bytes, fsspec] #fsspec a link to some remote storage like S3, Azure blog storage, local file, scp mounted file etc..
    error : Optional[Tuple(str, int, CallStack)]
```

You may also want to consider using TypeVar

So let's say you have a function that operates over lists of arbitrary objects so could be strings, ints, a context class whatever.

```python
from typing import List
T = TypeVar('T')
X = TypeVar('X')
def len_list(a_list : List[Tuple[T, X, int]]) -> int:
    # return length of list, the type of the value doesn't matter

```

The alternative is don't use types and just use Dicts and Tuples so for example you can have a dict like

```python
contex = {"name" : "name", "code" : 5, "device" : Device.CPU}
```

The reason this isn't so great is because you need to 
1. Build some constructor
2. Build additional functions for things like equality, delete, comparison, updates etc..

It's much easier to do this using classes


## Composability

Let's say you have functions that look like

```python
def a(x : str) -> float:

def b(z : float) -> Tuple[str, int]

# the function c is valid
def c(a : str):
    return b(a(x))
```

So you know what composes without knowing anything about the code

## Testing

Very useful to have types in tests

```python
# In file test_function.py
def test_answer(a : int = 5, b : int = 3) -> int:
  return a + b  
```

In Pyhon any file that starts with `test_` is a test file and you can run it using `pytest .` which you can integrate into your CI like Github actions but also get warnings around possible type checking issues by using `mypi .` or `pyre .`

## How to type your project

You're gonna be in 2 different situtations either you're starting a project from scratch or you're contributing to an existing project.

If from scratch use types and tests early and package your app as early as possible because if your app was useful to you odds are it'll be useful to someone else.

If you're contributing to an existing project - do not submit a single PR that types and lints the entire codebase - noone will approve it. You have two ways of doing it
1. Find the longest files in a repo -> you can run `cloc .` to get a list
2. Find the files that are changed most often because that's where safety is more important `git log --name-only --pretty="format:" | sed '/^\s*$/'d | sort | uniq -c | sort -r | head`


## Cool ideas

Other interesting type systems in Python are multiple dispath in FastCore
@overload where you can create conditional type systems to say something like if input is of type int then output is of type list of int but if input is of type list then output is of ype string


How to implement a typed system for numerical linear algebra

```python

# Scenario 1
a = torch.randn(50,100)
b = torch.randn(50,50)
a * b -> should give a type error


# Scenario 2 is 

class Model(torch.nn.Module):
    def __init__(self):
        ...
    
    def forward(self, a : Tensor[-1, 50]): # first input is of arbitrary dimension because it's a batch size and second dimension = 50 to make the matrix math work out
        # do the math

```

Maybe do another stream on this https://github.com/patrick-kidger/torchtyping