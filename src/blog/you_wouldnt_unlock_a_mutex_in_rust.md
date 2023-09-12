---
title: You Wouldn't Unlock a Mutex!
date: 2023-04-14
description: Did you know that you can't unlock a Mutex in Rust?
collections:
  - tech
  - rust
tags:
  - featured
  - WIP
  - rust
  - tech
---

Did you know that you cannot unlock a Mutex Lock in Rust?

Mutex unlocks are implicit in rust - this means that you cannot explicitly unlock a mutex in Rust, and that the `Mutex<T>` does not implement an unlock method in Rust.
This means that you need to be _really_ careful about how you lock and unlock Mutexes in Rust.

Consider the usecase of a threadpool - threadpools use parallelism to increase throughput in a request-based system like a web server. This allows the server to work on multiple requests in Parallel. So this usually requires incoming requests to be placed into a queue for workers to consume from.

In a multi-threaded system, each worker thread must occasionally check this queue to see if there are jobs on the queue, and must pick one job off the queue to handle. This allows multiple threads to work on jobs in parallel, reasonably(include amdahl's law ref) increasing throughput of the system.

To do this, we use a Mutex lock on the queue, to prevent that only one worker can remove one job from the queue at any given time. We do not care which worker thread it is, so long as there is only one. If we do not do this, we can have messy situations like two worker threads picking up the same job and working on it, leading to a host of problems like overwritten responses, processing it twice overall, etc -> so the worker thread's job is basically

- Check the queue - see if there are any jobs for me
- Pick the first available job for me
- process the job

At times like this, the question of "how long does a worker thread hold the lock on the queue" is of crucial importance to the throughput of the system. Ideally, you want to unlock as soon as you finish reading the task (usually a function pointer or the task description) from the queue. Especially in a web server, since it isn't easy to predict how long each request will take, it's better to unlock as soon as you consume the job, and not hold the lock until the job is done executing. This allows other threads to get their jobs faster.

Funny thing is in Rust there's no way to explicitly unlock a mutex - it automatically unlocks when the lock falls out of scope. Now you might say -

> That's stupid!

And I'll agree with you - but idiomatic rust will show up and smack us both on the head. Idiomatic Rust basically encourages this to happen implicitly.

```rust
// This causes contention! `held_lock` is not going to be left alone until the task is
// finished.
let held_lock = channel.lock().unwrap();
let current_task = held_lock.recv().unwrap();
```

In this case, the lock denoted by `held_lock` is going to remain held until it reaches the end of the current scope. Assuming you'll be executing your task after this, it's bad news for the performance.

But.. what if you could somehow _fool_ the lock into thinking it's reached the end of the scope? Rust also allows you to do this by introducing arbitrary scoped blocks wherever you want. So we just put the lock into the scoped block, and the mutex is released once the block is dropped!

```rust
// This does not cause contention! You're using a custom scope block to drop the
// MutexGuard, hence unlocking the channel consumer, and letting other threads get jobs
let current_task: Job = {
	let held_lock = channel.lock().unwrap();
	held_lock.recv().unwrap()
};
```

Without proper error handling, you're more likely to do this when you're prototyping. If you haven't read [this](https://blog.burntsushi.net/rust-error-handling/) then you're also likely to put it in prod.

```rust
// This is the same as the above - because the MutexGuard that's returned after the
// first unwrap is dropped after the recv method
let current_task = channel.lock().unwrap().recv().unwrap();
```

idk why this decision was taken. It appears to be [something of minor controversy](https://github.com/rust-lang/book/issues/1871), but as of right now the correct way (sans `.unwrap`) appears to be the scoped block option.
