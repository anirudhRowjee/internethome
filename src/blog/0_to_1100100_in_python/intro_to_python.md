---
title: Basics of Python 🐍
date: 2020-10-15
description: In this post, we take a look at the Python programming language, and some basic concepts, such as I/O, Variables and Data Types
tags:
  - python
  - beginners
  - tech
---

# Wikipedia Says...

Let's see what Wikipedia[^1] has to say about Python

> Python is an interpreted, high-level and general-purpose programming language.

Put your surgical gloves and mask on, for we are about to dissect this definition -

## Interpreted

So how does this whole thing work? How do we write programs in a fancy version of English, and get computers to be able to understand them? Obviously, someone or something needs to translate what we write to some form of Binary Language. This is done by another program, usually known as a Compiler or an Interpreter. They do the same thing (convert the code into `1`s and `0`s), but aren't the same, as they differ fundamentally in how they do this job of translation.

When it comes to translating between natural languages, any person who has enough experience with both languages will be able to account for grammatical mistakes and convey the message, because they can understand the _essence_ of what you are trying to say - which makes it okay for us to not speak in 100% grammatically correct language.

Computers, however, cannot understand the _essence_ of what we are trying to say, which is why the languages we speak to the computer with comes with a very strict rule set - this is known as its **Syntax**.

While Compilers and Interpreters translate the code we write into machine code, they also notify us of syntax errors, which is a very important job. Thus, compilers and interpreters report errors differently. This is because a compiler reads all the code you wrote, and then translates it to `1`s and `0`s once it makes sure there are no mistakes.

An Interpreter reads and executes your code **line-by-line**, and will only stop the program when it reads an error. Alternatively, a Compiled programming language will notify you of all the errors, wait for you to fix them, and only then execute the code. This step of error-checking and final translation to machine code is known as _Compiling_. Here's a relevant XKCD -

![XKCD Comic 303 - compiling](https://imgs.xkcd.com/comics/compiling.png)
_Credit - [XKCD Comic 303 - compiling](https://imgs.xkcd.com/comics/compiling.png)_

The creators of Python decided to take the Interpreter approach, and we will learn soon enough that it has its own positives and negatives. This is why Python is known as an Interpreted Langauge.

## High level

Do you remember the control-complexity tradeoff that we mentioned in the previous post? In essence, the more "complex" a programming language is, the more control you have over what's happening. This is where the concept of a high-level programming language comes in. High level languages are less verbose, but do a lot of work for the programmer by making assumptions and having a fixed way of doing things (think of our coffee example).

Consider the "Hello, world" program in Assembly - the closest you can get without to writing binary without actually writing binary. It looks something like this -

```nasm
section     .text
global      _start                              ;must be declared for linker (ld)

_start:                                         ;tell linker entry point

    mov     edx,len                             ;message length
    mov     ecx,msg                             ;message to write
    mov     ebx,1                               ;file descriptor (stdout)
    mov     eax,4                               ;system call number (sys_write)
    int     0x80                                ;call kernel

    mov     eax,1                               ;system call number (sys_exit)
    int     0x80                                ;call kernel

section     .data

msg     db  'Hello, world!',0xa                 ;our dear string
len     equ $ - msg                             ;length of our dear string
```

And this is what it looks like in Python -

```python
print("Hello World")
```

It's okay if you didn't understand any of it. As you can see, as we go from Assembly to Python, the simplicity of the code increases with the number of assumptions the Programming Language makes. Python is a high-level language because we don't have to focus on Computer Memory, Syscalls, Registers (like we had to do in Assembly) - the language has standard methods of doing these things, and we don't need to care for it.

## General Purpose

This just means that the programming language can be used to do anything - in that its functionalities aren't limited to what you're using it for. Most modern programming languages are general purpose. Conversely, some programming languages are written to perform specific tasks - such as MATLAB for numerical computing, COBOL for business use, and Verilog for modeling electrical systems. Python can do everything that these langauges can, but the converse is not true, which is why Python is a general purpose programming language.

# How to use the REPL

As you all know, we will be using the REPL from [repl.it](repl.it) to run all the code we need to in this series. If you're already familiar with this, you can skip this section.

**If you want to track your progress, don't forget to sign in to your account on [repl.it](repl.it).**

Okay. Now, say Hello!

Press the Green **Play Button** to run the code. When you do this, the Python Interpreter will execute the code written below `main.py` line-by-line. You will see the output of this code in the panel below that, where something like `Python 3.8.2 (default, Feb 26 2020, 02:56:10)` should be written. This is the actual `REPL`, but we'll be using the approach we discussed to execute all our code.

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-helloworld?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

When I ask you to write or change something, I mean that you should write it just like you would write any other text or word document in the area directly below `main.py`. When I ask you to run the code, just press the green play button and watch the magic happen.

# Input/Output

Like we established in the previous post, we need to be able to communicate with the program (and by extension, the computer) to make anything happen. In this section, we will learn how to perform input and output operations in Python.

## Handling output with `print()`

::: tip
This is similar to the [`Say` construct](https://blog.anirudhrowjee.xyz/posts/0_to_1100100_in_python/a_gentle_introduction_to_programming/#communication) from the previous post.
:::

Hey, we know this! We used this in our "Hello, World" example. The syntax for using this is as follows -

```python
print("we can put whatever we want here")
```

We can use `print()` to literally "print out" (akin to using a printer) anything that is enclosed within the brackets after the word print.

Take this for example -

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-output-1?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Hit the green play button, and look at the REPL for the output. Awesome, isn't it? The computer speaks to us!

The output should read as follows -

```
Here, I am printing some text. I will print a number next
123456
123.456
```

There! now we know how to use `print()` - but did you notice something curious? Everything is printed one below the other. If you're new to programming, this won't confuse you, because this is how you expect it to be. However, in other programming languages, to make the `print()` put stuff one line after the other, you require a special character called an escape sequence (`\n`). Thankfully, this is the default in Python.

Let's see if we can change that. In the REPL above, find the line of code which reads

```python
print(123456)
```

and change it to

```python
print(123456, end='')
```

Run the code now.

What is this? They're on the same line! It looks like this now.

```
Here, I am printing some text. I will print a number next
123456123.456
```

Finally, modify that same line of code to read as below, and run it again.

```python
print(123456, end='\n')
```

Everything is back to normal!

Checkmate, Java/C++/C/PHP/(insert language here) programmers!

::: tip
the `\n` part is an [ASCII Escape Sequence](https://en.wikipedia.org/wiki/Escape_sequence), and can be used across programming languages to print out a new line. Click on the link to find out more examples of such sequences.
:::

In summary, we learned how to use `print()`, so that our computer can talk to us. We also learned how to print things on the same line, and we learned how to change what the last thing printed at the end is (with the help of our friends over at `end=`).

## handling input using `input()`

Okay, great! Now that we can get the computer to talk to us, let's see how we can get it to ask us the questions that matter.

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-input-1?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

(notice the use of the escape sequence to make a new line appear. Remove it and see what happens!)

Run the code, and write your name in the REPL, where you should see a solid block cursor. Then press enter. Your output should be something like this (unless you're named Anirudh too, in which case, it should be exactly like this)

```
What is your name?
anirudh
very nice.
```

And that's it. That's all there is to `input()`. Why? This is because we need some mechanism to **remember** the value we get from the user. This makes for a good segue into our next segment, which deals with just this.

# Variables and Data Types

When we talk about storing data, each language does it differently in terms of the specifics - but they all use the concept of a **variable**. As the name Indicates, a variable is something that "can change" or "can be different things". Variables are used to hold data that is specific to the lifetime of the program - something like input from a user, or a number of times you need to do something.

To put it in context, consider how Humans remember things. We all have short-term as well as long-term memory. The long-term memory is like the hard disk of a computer - all the important, permanent information is stored there (like your name, your address, your phone number, parents' name, etc) - and the short term memory is the more volatile, more situational memory - things you only need to remember for a limited amount of time (What the time is right now, what you were daydreaming about, etc), and is mostly stuff you won't remember after you start something else.

![Floppy Disk Image](https://images.unsplash.com/photo-1533279443086-d1c19a186416?ixlib=rb-1.2.1&auto=format&fit=crop&w=1189&q=80)
_Photo by [Fredy Jacob on Unsplash](https://unsplash.com/photos/t0SlmanfFcg)_

The computer equivalent of short-term memory is known as RAM or Random Access Memory. All our variables are stored in the RAM (also known as just memory), which means that just like you forget that your mom told you to turn off the stove as soon as you start scrolling through Instagram, your computer cannot save data that was there in the RAM when it turns off.

This is why you must save your Word Document or Powerpoint, because it is loaded into the memory for you to make modifications, and written to the hard disk when you press save.

The main difference, though, is this - when I ask you to remember something, you don't need to remember anything to identify it by. However, for computers, this is a problem - if you need to remember something that you want to access in the rest of the program, you need to give it a name.

## Declaring A Variable

When we create a variable and we assign a value to it, it is known as **declaring** or **initializing** a variable. This tells the interpreter to keep this value in the computer's short-term memory so that the program can access it later.

The basic syntax is as follows

```python
variable_name = "value"
```

In the REPL Below, we learn how to declare and print variables.

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-variables-1?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

::: tip
Everyone has a different idea for what the "right" way is to name a variable. In my opinion, as long as the variable name describes the reason/purpose for the variable to exist (names like `foo`, `something`, `hello`, `lol` are strict no-nos in my book) and they [conform to the rules for naming variables](https://www.w3schools.com/python/python_variables.asp), they're okay.
:::

Wait a minute! Can we remember the answer to user input as well? Yes, we can. Finally, we start to see a resemblance to the `Remember` construct we used in our pseudocode, so that we can remember the name of the programmer.

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-variables-2?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Press run, type in your name, and hit enter! The computer should be greeting you by name now. Feels nice, doesn't it?

::: tip
for those of you curious about what I did with the `print()` block to make both come on the same line without using `end=` or anything, I'll explain it in a bit. For now, just consider this the default way of doing things.

Using this method to join two values works only in `print()`. I'm sorry for the obscurity, but I promise, all in good time.
:::

Lastly, let's build the calculator! We can do this because we know

1. How to take input from users
2. How to add numbers
3. How to output variables and numbers to the screen

"Are you ready, kids?"

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-variables-3?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

When you're done filling in the figurative blanks, the output should look like this for everything except for the last line of code -

```
Input the first number
<you type the first number, say, 1>
(hah, nice try. Your prompt comes here)
<you type the second number, say, 2?
your result is
12
```

Wait, what? 1+2 isn't 12! But.. but.. the program didn't report an error! What happened?

Open the REPL part of the REPL above - the terminal where it says `Python 3.X.X`, and type the following

( the `>` is the fancy symbol in red color that you see in your REPL. Don't type it out )

```
> 1 + 2
3
> "1" + "2"
'12'
```

This is because of the **Data type** of the input that we have given. Programming languages can support many types of data, such as text, numbers (integers, complex numbers, and decimal, also known as floating-point numbers). Most programming languages also support storing a continuous series of things - such as, say, the price of a stock for a week of the stock market is open. This is known as an **array** or a **list**, and we'll explore some more of it in the next post. Python also has **dictionaries**, where you can store a value and give it a name. You can also make your own data types, which is something we'll explore in the next few posts.

We'll fix the issue with our calculator in a little bit, but first, let's understand why this happens.

## Data Types

At this point, we've understood the advantages of storing data, and even previously with the `Remember` block. We know that it's temporary, and won't last in between shutdowns. Managing memory for data and other things is a huge part of learning a more classical language like `C` or `C++`, but thankfully, we don't have to worry about that with Python.

Different programming languages store data in different ways, but one thing you'll find common across all of them is that there are different types of data that they store. Like we saw before, we can print numbers, text, decimal numbers, etc. For us to use these, the programming language has to allow them to exist, right? Thus, most data is classified into types. We do not program in data, but we do use programming to manipulate data that we have, or that we get from the user.

Python has a few primitive data types, as you already know - and when we declare a variable with some value, the variable is said to be "of the type of the value". So, if you declare a variable like this -

```python
my_variale = 10
```

then `my_variable` has the same data type as its value, making it an integer type variable.

(feel free to open a REPL and type the following snippets out, just to verify the output).

1. Integer Data Type - used to store integers

```python
my_number = 10
print(my_number)
my_other_number = 20
print(my_other_number)
result = my_number + my_other_number
print(result)
```

This should give you something like

```
10
20
30
```

Keep this in the back of your head right now.

2. Float Data Type - used to store decimal numbers

```python
my_float = 100.123
print(my_float)
```

as you can expect, the output will be -

```
100.123
```

3. String Data Type - used to store text or letters

Learning this by example is the best way to do this. Fill in your first name and your last name, and remember that String type variables should always have quotes around them, single or double. (`'` or `"`)

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-datatypes-1?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Did I just add two strings? Yes, I did! In other programming languages, and generally, this process is known as **concatenation**.

### Fixing the problem

So, do you have any idea as to what went wrong in our calculator application? If you need some help, I will point you in the direction of the [official documentation for `input()`](https://docs.python.org/3/library/functions.html#input).

If you missed it, we can see that when two string variables are added, they are concatenated - whereas when two integer variables are added, they give you the mathematical result of the addition.

🕵

Could it be possible that the numbers we asked the user for are being stored as string variables? If you thought so too, you hit the nail right on the head! That's exactly what's happening. Using `input()` always asks the user for a string, and stores the result as a string - making it **return a string**. The terminology is a bit confusing for now, but we'll address it more when we address the topic of functions in the next post.

So, we need to make sure that we are getting numbers from the user (i.e. Integer Data Type variables) so that we can add them as numbers, not as Strings. To do this, we will use something known as **Type Conversion** or **Type Casting** - Just as Harry Potter casts a spell, we can cast - "make" - a variable a specific type. Take this, for example -

<iframe height="400px" width="100%" src="https://repl.it/@AnirudhRowjee/0to1100100python-datatypes-2?lite=true" scrolling="no" frameborder="no" allowtransparency="true" allowfullscreen="true" sandbox="allow-forms allow-pointer-lock allow-popups allow-same-origin allow-scripts allow-modals"></iframe>

Your output should be as follows

```
1020
30
```

Here the first output was the result of just adding the strings, whereas the second was the result of adding our two variables as integer variables. Here is the [official documentation for `int()`](https://docs.python.org/3/library/functions.html#int), if you want some more clarity. The first line of the documentation should be enough help, which roughly describes what I just wrote above -

> Return an integer object constructed from a number or string x, or return 0 if no arguments are given.

Now, here's the challenge - since you already know the following -

1. Taking Input from users
2. Converting from String data type to Integer data type
3. Re-declaring variables with new data

You can now fix the calculator program that we wrote above. If you look closely, all the code you need is already provided to you 👀

# What's Next?

That's it for today! Pat yourself on the back. This post is usually the biggest hurdle that new learners deal with, which is making the connection between the pseudocode and the actual code. Give yourself credit for taking the initiative, and coming this far. Great Job!

In the next post, we will cover some more abstract topics, such as functions, loops and conditionals. It's going to be a bit more difficult to digest, but at that point, once you understand these, you will be able to do a lot more with Python.

# Bonus Assignment

Write a program that will take as input two numbers, `a` and `b`, and print out the remainder you get when you divide `a` by `b`. Using google, Python documentation and websites such as Stackoverflow (not to directly copy the code, but to get help on what you're stuck on), is encouraged.

Send me an email with the link to the REPL.it that you used to write this program, or just send me an email with the code.

I hope you liked this post and had as much fun learning from it as I had while writing it. See you all in the next one! ✌

[^1]: [Python](<https://en.wikipedia.org/wiki/Python_(programming_language)>)
