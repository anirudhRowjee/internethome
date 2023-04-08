---
title: The CAP Theorem, or how I learned to stop worrying and love the distributed system
date: 2022-05-09
description: Let's take a look at the CAP Theorem.
collections:
  - research
tags:
  - databases
  - distributed_systems
  - CAP
  - WIP
  - computerscience
---

## Context

I really need to write this post.

- what's a distributed system?
- why would you need one, especially when it comes to data

## The Story So Far

- introducing the setup we're considering - the distributed python dictionary, containing account balances

## Introducing the Constraints

let's talk about what each of these terms mean.

### Consistency

Every read recieves the most recent write - or an error

- strong vs weak consistency, talk about read replicas drifting from master

### Availability

A node that has crashed is not considered to be suitable for an evaluation of availability. - Lynch

Each request recieves a response.
case 1 - no errors are allowed except for that of a consistency failure
case 2 - no errors at all, but no guarantee of recieving the latest write

### (Network) Partition Tolerance

Networks are an inherent part of distributed systems. However, a partition-tolerant system must continue to function "despite an arbitrary number of messages being dropped or delayed by the network in between nodes"

## The Theorem

Explain that there's a tradeoff to be made.

## Solutions

CA databases (average joe postgres, read https://codahale.com/you-cant-sacrifice-partition-tolerance/)

CP (low availability, MongoDB - why?)
AP (Cassandra - why?)

### PACELC

### PIE

https://www.alexdebrie.com/posts/choosing-a-database-with-pie/

### Do you need to distribute?

## references

https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html
