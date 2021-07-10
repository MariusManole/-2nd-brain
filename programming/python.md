# Python

## Misc notes from "100 days of code"

### Function arguments - \*args, \*\*kwargs

Two ways of extending the number of arguments accepted:

* positional arguments - a tuple captured and stored in **\*args**
  * _in function definitions, use as an iterable \(**for n in args**\)_
* keyword arguments - a dictionary captured and stored in **\*\*kwargs**
  * _in function definitions, retrieve via **kwargs.get\("keyword\_name"\)**_

```text
def add(*args):
    print(type(args)) # <class 'tuple'>
    sum = 0
    for n in args:
        sum += n
    return sum


def calculate(n, **kwargs):
    print(type(kwargs))  # <class 'dict'>
    n += kwargs.get("add")
    n *= kwargs.get("multiply")
    print(n)


add(1, 2, 3 ,4)
calculate(5, add=3, multiply=5)
```

