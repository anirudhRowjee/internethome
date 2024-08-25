---
title: Refactoring a Large Function in Go
date: 2024-03-23
description: I write about a challenge I faced at work, and how I solved it
collections:
  - tech
  - rust
tags:
  - featured
  - WIP
  - tech
  - rust
---

So, you've finally done it! You've shipped a PoC, and the dust has finally
settled; your boss, your boss's boss, and your boss's boss's boss, all like what
you're working on. Hooray!

You can now finally stop thinking about your code as a pencil sketch, and you
can now finally start painting. You can afford to now pay attention to the finer
details - what is this function supposed to be called? Do we really need all the
data in this struct? In other words, you can now start refactoring.

> _“Any fool can write code that a computer can understand. Good programmers
> write code that humans can understand.” ~ Martin Fowler_

A well-timed refactor can save you a lot of time and energy; we've all been in
the situation where we've looked at code we've written a while back, and have
understood _none_ of it. Context - the reason you can get away with giving
variables single-letter names - fades, and leaves behind a massive void. By
refactoring your code for maintainability, you're looking out for your future
self!

Now that you've been convinced that this is important, let's walk through an
example. The goal here is as follows -

> Refactor a large function into multiple smaller functions with minimal
> breakage

## The Problem

Consider the following function, built by our friendly neighbourhood widget
factory elves. As you can see, they're doing their best, but in the interest of
iteration, they've ended up with a massive, massive function.

```go
// main.go
package main

import "fmt"

func FizzBuzzOverkill(limit int) ([]string, error) {
	var final_output []string
	for i := 0; i < limit; i++ {
		output := ""
		div_three := false
		div_five := false
		// check for divisibility of three
		if i%3 == 0 {
			div_three = true
			output += "fizz"
		}
		if i%5 == 0 {
			div_five = true
			output += "buzz"
		}
		if !div_three && !div_five {
			output += fmt.Sprintf("%d", i)
		}
		final_output = append(final_output, output)
	}
	return final_output, nil
}
```

## The Solution

How messy! Certainly, we can do better than this - but refactoring without
tests, atleast end-to-end tests, is a bad idea! you need some baseline for
correctness.

```go
// main_test.go
package main

import (
	"testing"
	"golang.org/x/exp/slices"
)

func TestFizzBuzz(t *testing.T) {
	sample_output := []string{"fizzbuzz", "1", "2", "fizz"}
	generated_output, err := FizzBuzzOverkill(4)
	if err != nil {
		t.Fatal(err)
	}
	if !slices.Equal(sample_output, generated_output) {
		t.Errorf("Fizzbuzz Failed!")
	}
}
```

There we go! Given that this test passes, we can now start to begin refactoring.

The guiding idea here is that you _do not_ attempt to rewrite it all at once;
you isoldate the codebase into blocks, convert each function into a block one by
one, and then slowly repalce blocks with their respective code. At the end of
each integration, run the test to see if it's working well!

Let's see how we do that. In the example here, we can see three major tasks
within the loop:

1. Check for divisibility by 3, and
2. Check for divisibility by 5, and
3. Add the number itself if it isn't divisible by either

Given that we've identified the major working units of the code, let's begin by
pulling out these parts.

An important point to note is these refactors work better, and more
specifically, this method, works better when you majorly deal with pure
functions. We don't want any ghosts in the shell!

```go
// Function to add "fizz" to the string if it's divisible by 3
func ModifyStringIfDivisibleBy3(number int, output_string *string) {
	if number%3 == 0 {
		*output_string += "fizz"
	}
}
```

This is slightly convoluted, but you get the idea. Let's now integrate it into
the main codebase.

```go
// main.go
func FizzBuzzOverkill(limit int) ([]string, error) {
	var final_output []string
	for i := 0; i < limit; i++ {

		output := ""
		div_three := false
		div_five := false

		// check for divisibility of three, set div_three to true if it works
		div_three = ModifyStringIfDivisibleBy3(i, &output)

		if i%5 == 0 {
			div_five = true
			output += "buzz"
		}

		if !div_three && !div_five {
			output += fmt.Sprintf("%d", i)
		}

		final_output = append(final_output, output)
	}
	return final_output, nil
}
```

running our tests -

```shell
anirudh@shatterdome:~/projects/fizzbuzz-overkill  $ go test .
ok      fbo     0.001s
```

We are now good to go! We can now shamelessly refactor. Let's change, piecewise,
the other two segments of the program.

Let's remove the divisibility check for five -

```go
// Function to add "fizz" to the string if it's divisible by 3
func ModifyStringIfDivisibleBy3(number int, output_string *string) bool {
	if number%3 == 0 {
		*output_string += "fizz"
		return true
	}
	return false
}

// Function to add "fizz" to the string if it's divisible by 3
func ModifyStringIfDivisibleBy5(number int, output_string *string) bool {
	if number%5 == 0 {
		*output_string += "buzz"
		return true
	}
	return false
}

func FizzBuzzOverkill(limit int) ([]string, error) {

	var final_output []string

	for i := 0; i < limit; i++ {
		output := ""
		div_three := false
		div_five := false

		// check for divisibility of three
		div_three = ModifyStringIfDivisibleBy3(i, &output)
		div_five = ModifyStringIfDivisibleBy5(i, &output)

		if !div_three && !div_five {
			output += fmt.Sprintf("%d", i)
		}
		final_output = append(final_output, output)
	}
	return final_output, nil
}
```

```
anirudh@shatterdome:~/projects/fizzbuzz-overkill  $ go test .
ok      fbo     0.002s
```

And now, lastly, the catch-all.

At this point, it's a slightly controversial take - I think it's okay to leave
it as is at this point. This may be biased by this particular example, but i'm
trying to promote responsible refactoring and I think you shouldn't do anything
more to this. It's _okay_.

Eliminating the redundant variables, we get:

```go
func FizzBuzzOverkill(limit int) ([]string, error) {
	var final_output []string

	for i := 0; i < limit; i++ {
		output := ""

		div_three := ModifyStringIfDivisibleBy3(i, &output)
		div_five := ModifyStringIfDivisibleBy5(i, &output)
		if !div_three && !div_five {
			output += fmt.Sprintf("%d", i)
		}

		final_output = append(final_output, output)
	}
	return final_output, nil
}
// The line breaks added are purely for aesthetic reasons!
```

## Why this?

The advantage of this approach is that you get to keep your refactoring process
alive, while also ensuring your code works. It seems somewhat rudimentary now,
but it was definitely helpful to me when I had to refactor a large function.

Happy Refactoring!

```
\m/
```
