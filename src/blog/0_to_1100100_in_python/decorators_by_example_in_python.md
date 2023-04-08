---
title: Decorators by Example in Python 🐍 🍩
date: 2021-02-03
description: Trying to understand what a decorator in Python is, and how to use it
tags:
  - python
  - beginners
  - featured
  - tech
featured: true
collections:
  - tech
---

# Functions inside functions

First of all, let's get one thing clear. In Python,

**_It is possible for you to define functions inside functions, and return said functions_**.

Consider this REPL, for example -

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/blogdecoratorsfunctionsinsidefunctions?lite=true" scrolling="no" frameBorder="no" allowTransparency={true} allowFullScreen={true} sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Upon hitting run, we see

```python
> <function return_hello_world.<locals>.hello_world at 0x7f0f417a8a60>
```

(you can uncomment the line below to see the function being executed.)

What Python is telling us here is that we are able to return not just strings, lists, dictionaries, tuples, but also whole functions themselves! What scenarios would this be useful in?

1. You need a function that is dependent on the context it is called in, but you do not want to repeat the same set of `if-then` statements again and again
2. You want a `callback` to be returned, based on which it is easier for you to perform conditional operations, and execute the results right away instead of looking up an action.

You can also pass a function as an argument to another function! Consider the famous example of the `map` function, which requires you to pass a function as an argument - which it then applies to every element of one or more iterables.

In this specific case, we are passing the function `get_first_2_letters` as an argument for `map` to apply to the entire list.

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/blogdecoratorsfuncasargument?lite=true" scrolling="no" frameBorder="no" allowtransparency="true" allowFullScreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

This gives us the output

```
['BANGALORE', 'DELHI', 'MUMBAI', 'CHENNAI', 'KOLKATA']
['BA', 'DE', 'MU', 'CH', 'KO']
```

# Adding Functionality to a function

Now that we know all this, we have to consider a situation where we might want to augment the functionality of a function that, say, someone else defines. Think about it this way - You're running some code, and you're using `print()` to peform a stack trace of sorts - so your output looks something like this -

```
Doughnut 1
> Debug
Applying glaze.. 10%
Applying glaze.. 20%
Applying glaze.. 30%
Applying glaze.. 40%
Applying glaze.. 50%
Applying glaze.. 60%
Applying glaze.. 70%
Applying glaze.. 80%
Applying glaze.. 90%
Applying glaze.. 100%
> End Debug
Doughnut 2
> Debug
Applying glaze.. 10%
Applying glaze.. 20%
Applying glaze.. 30%
Applying glaze.. 40%
Applying glaze.. 50%
Applying glaze.. 60%
Applying glaze.. 70%
Applying glaze.. 80%
Applying glaze.. 90%
Applying glaze.. 100%
> End Debug
Doughnut 3
> Debug
Applying glaze.. 10%
Applying glaze.. 20%
Applying glaze.. 30%
Applying glaze.. 40%
Applying glaze.. 50%
Applying glaze.. 60%
Applying glaze.. 70%
Applying glaze.. 80%
Applying glaze.. 90%
Applying glaze.. 100%
> End Debug
...
```

At this point, you're using the following pretty often, and like all smart developers do, you want to ease some of your pain. Instead of manually adding print statements, you just want this to be a pretty easy change for you to make, so that you can debug pretty fast. You want any debugging output to be surrounded by the following

```
> Debug
<debugging output>
> End Debug
```

Your doughnut factory code looks something like this.

```python
# function to apply glaze to <number> doughnuts
def glaze():
    print("> Debug")
    for x in range(1, 11):
        # actual code to apply glaze comes here, we omit it for brevity
        # below this is the debug printing
        print(f"Applying glaze.. {x*10}%")
    print("> End Debug")


for x in range(1, 6):
    print(f"Doughnut {x}")
    glaze()
```

Instead of manually adding the following, you want to make it automatic.

```python
    print("> Debug")
    # function code comes here
    print("> End Debug")
```

Given what we know about functions, if we want to add this functionality to _any_ function we want, we could define another function that takes our function as an argument, and gives us another function which has all these additional things that we want.

```python
# code to make a function a debug function
def make_debug(function):

    # define an inner function here
    # we are not calling the function directly as we want to be able to execute
    # it like this again and again
    def temporary_function():
        print("> Debug")
        function()
        print("> End Debug")

    # send it back
    return temporary_function
```

Perfect! Now, whenever we want to scan the output to find the debugging output for any function, we can do the following -
(note that we have removed the `print()` statements from inside the function body, yet, if you run it, you get the same output)

```python
# function to apply glaze to <number> doughnuts
def glaze():
    for x in range(1, 11):
        # actual code to apply glaze comes here, we omit it for brevity
        # below this is the debug printing
        print(f"Applying glaze.. {x*10}%")


glaze = make_debug(glaze)

for x in range(1, 6):
    print(f"Doughnut {x}")
    glaze()
```

which should give you the same output as above.

```
Doughnut 1
> Debug
Applying glaze.. 10%
Applying glaze.. 20%
Applying glaze.. 30%
Applying glaze.. 40%
Applying glaze.. 50%
Applying glaze.. 60%
Applying glaze.. 70%
Applying glaze.. 80%
Applying glaze.. 90%
Applying glaze.. 100%
> End Debug
Doughnut 2
> Debug
...
```

# Time to Decorate!

At this point, it's perfectly okay for us to use the following syntax to make a function a debug function -

```python
some_function = make_debug(some_function)
```

but this syntax is tedious, and might lead to more problems (scope issues, or perhaps you forgot to redefine, etc). Thankfully, there is an excellent shorthand syntax that the creators of Python thought up for us - we can simply apply the syntax for decorators that you are more familiar with -

```python
# code to make a function a debug function
def make_debug(function):

    # define an inner function here
    # we are not calling the function directly as we want to be able to execute
    # it like this again and again
    def temporary_function():
        print("> Debug")
        function()
        print("> End Debug")

    # send it back
    return temporary_function

@make_debug
def some_function():
    print("hello, world!")

some_function()
```

which should give us as output

```python
> Debug
hello, world!
> End Debug
```

Congratulations! You have just made your first decorator. Note that removing the functionality of this decorator from a function is just a matter of commenting out the line above the function definition.

You can see the decorator in its formal syntax applied to the doughnut factory in this REPL -

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/blogdecoratorsdoughnutfactory?lite=true" scrolling="no" frameBorder="no" allowtransparency="true" allowFullScreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

# Advantages

Now that you know how to make a decorator (and how to easily add it to a function), you can do some pretty crazy things! Consider some of the usecases.

1. You just made a decorator that has a widespread usecase (sending logs to a logger, print formatting, etc) - applying it does not require you to mess with any of the code of the functions themselves.
2. You want to easily be able to make code changes to your functions (debugging, etc)
3. You want to share decorators (which is good, because you can share them as python programs!)

Lastly, know that decorators can be chained. You can use multiple decorators for a function, like so -

```python
@make_debug
@make_blue
@make_bold
def eat():
    # function to eat doughnuts
    print("yum!")
```

It is also possible to make decorators that take arguments, but we will not be exploring that usecase here.

That's it! you're now ready to start your own doughnut factory. Make me proud 😎
