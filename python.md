# Python

## *args and **kwargs

There are two symbols that can be used to define a variable number of arguments to a function. They are:

- ***args** (Non Keyword Arguments): Using a * before an argument implied there will be a variable number of them. This is passed into the function as a tuple that can then be iterated over.
- ****kwargs** (Keyword Arguments): Using a ** before an argument implies that there will be multiple named arguments. This will be passed into the function as a dictionary with key/value pairs representing variable name and value.

## Decorator Format

```python

# Decorator function
def example_decorator(fun):
    def _wrapper_function(*args, **kargs):
        # Can check function arguments here if needed
        if some_condition():
            # Do something else, maybe modify arguments
        else:
            func(args, kargs)

    return _wrapper_function

@example_decorator
def function_to_decorate(name, type, dry_run):
    # Do some stuff

```