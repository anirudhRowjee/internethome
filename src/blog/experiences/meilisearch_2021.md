---
title: Fixing a Bug in Meilisearch
date: 2022-01-10
description: How I made my first Hacktoberfest 2021 Contribution in Rustlang
collections:
  - foss
  - tech
tags:
  - foss
  - hacktoberfest
  - rust
  - web
  - tech
  - featured
featured: true
---

In October 2021, I got my first pull request in a major open-source project merged. Here's what happened, and how I did it -

## What's Meilisearch?

[Meilisearch](https://github.com/meilisearch/MeiliSearch) is a "powerful, fast, open-source, easy to use and deploy search engine. Both searching and indexing are highly customizable. Features such as typo-tolerance, filters, and synonyms are provided out-of-the-box".

To put it simply, Meilisearch allows you to implement fast search at scale, and does it in [Rust](https://www.rust-lang.org/). Take a look at [this GIF](https://github.com/meilisearch/MeiliSearch/blob/main/assets/trumen-fast.gif) and see it in action. It's safe to say it blew my mind the first time around.

## What was the Issue?

- Find the issue itself [here](https://github.com/meilisearch/MeiliSearch/issues/1750).
- Find the Pull Request to close this issue [here](https://github.com/meilisearch/MeiliSearch/pull/1755).

Meilisearch has a browser-accessible dashboard that helps you test the implementation as and when you install it. This dashboard is usually how most people first interact with Meilisearch, and it's where the demo GIF comes from. Given the above, it's easy to understand why it's a critical user-experience component of the application.

I, on the other hand, was (and still am) a curious rustacean looking to better my knowledge, so I was naturally interested when I saw this issue open with the `good first issue` label. I jumped at the chance to figure this out, and then I cloned the repository. It's the largest codebase I've interacted with, and I was naturally somewhat intimidated by what was going on.

This repository didn't deal with the core implementation of search - that's taken care of by [milli](https://github.com/meilisearch/milli/) - to quote, "The code in this repository is only concerned with exposing the HTTP API, managing multiple indexes, and handling the update store".

After I cloned it, I got started with trying to fix this error. **There were no error messages while this project ran**, which meant that this bug would be harder to sniff out.

## The Debugging Process

All I had to start with were a bunch of errors in the browser console.
![browser console describing the error](https://user-images.githubusercontent.com/7032172/135531091-5ce938ef-5ebf-485e-8c18-2beec258e1b6.png)

From my experience with frameworks such as Django and Flask, This usually meant some file wasn't being served properly. Here's what I did to dig a little deeper -

1. I Ran the test suite, and All the tests passed

   This was simple enough - all I had to do was run

   ```shell
   $ cargo test
   ```

   Rust has some pretty neat features baked directly into the toolchain, which helps you write tests pretty easily. As you see above, it's also super easy to run a test suite.

2. Investigated the built output

   I then proceeded to build the project, for which all I had to do was run

   ```shell
   $ cargo build
   ```

   I found the build output at `/meilisearch-http/target/rls/debug/build/meilisearch-http-a6e2793cb../out/mini-dashboard/`. This consisted of a bunch of static files pulled from an external source, which were then served using the [Actix](https://actix.rs/) Web Framework.

   Since there were a bunch of static files wired up, I used the npm package `serve` to run a temporary web server here, which served all files correctly.

   ```shell
   # if you haven't got it installed already
   $ npm install -g serve # installs this globally
   $ serve
   ```

   This confirmed my initial suspicion that it **wasn't a problem with the dashboard itself, but rather with how it was being served.**

3. I then Looked into how the server was setup.

   At this point I used simple print debugging to ensure that the paths were generated correctly, along with their MIME types. This, too, is relatively simple - you can use the `println!()` macro to easily print the resource in question.

   All seemed to be in order. I also tried using the `format!` macro to prepend a slash onto the file path for each file, but this didn't make a difference at all.

4. At this point I noticed that the `index.html` file was the only file being served correctly.

   Taking a look into the route configuration, I noticed that the route for the index file was configured like this -

   ```rust
   config.service(web::resource("/").route(
       web::get().to(move || HttpResponse::Ok().content_type(mime_type).body(data)),
   ));
   ```

   as opposed to

   ```rust
   scope = scope.service(web::resource(path).route(
       web::get().to(move || HttpResponse::Ok().content_type(mime_type).body(data)),
   ));
   ```

   Given I'm not too familiar with Actix, I took the approach of registering the routes for the static files the same way as the `index.html` file, replacing the above block with the following to serve static files -

   ```rust
   config.service(web::resource(path).route(
       web::get().to(move || HttpResponse::Ok().content_type(mime_type).body(data)),
   ));
   ```

This worked! `localhost:7700` now produced the dashboard properly.

![An Image of the Meilisearch dashboard working](https://user-images.githubusercontent.com/42117791/135704477-c39912c1-78f6-4b7b-aa0b-aecfb06f86fd.png)

## Why did this change work?

Given I haven't explored Actix in depth, I'm probably not the best person to answer this question, but if I had to take a guess, I'd say it has to do with how Actix internals decided route registration for static assets should take place. It also probably calls for special handling of the index page at `/`, and this caused the issue.

I'll be back with more information on this when I've learnt more!

## Wrapping Up

This was my first contribution of any sort to a major open-source project, and I'm glad it was a Rust project! It's a really cool language, and I'm glad I got to learn more about it. The maintainers were super nice, and this made me feel welcome! I was happy when this PR got merged, and have, since then, felt a little more confident about my ability to navigate large codebases which I have no familiarity with. This was fun!

I look forward to contributing to more, larger open-source projects!
