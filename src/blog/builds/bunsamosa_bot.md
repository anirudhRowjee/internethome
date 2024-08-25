---
title: Building Github bots with BunSamosa Bot
date: 2022-12-03
description: Building a Golang-based Github Bot for ACM PESUECC Hacknight
tags:
  - builds
  - golang
  - WIP
  - tech
  - featured
collections:
  - tech
---

There's nothing like the feeling of being in an environment where everyone's
focussed and working on the same thing at the same time. To me, this is one of
the most invigorating environments to be in. There's so much ideation and
creativity in the air that taking a breath inspires you to do more and be more.

One example of such an atmosphere is the 24 Hours of what we at PESU EC Campus
call "Hacknight", an annual FOSS Festival organized by ACM PESUECC. It's 24
hours of pure hacking and problem-solving, where many students take their first
steps in the world of open-source software by making their first contributions
on Github.

## What's Hacknight?

Here's how the event works -

1. Students sign up to participate in the event
2. The student developer community at PESU ECC Opens up a few repositories
   (small projects they've worked on before or have been working on) to the all
   the participants by making the repository public
3. Each repository has issues of varying difficulties present on it, and each
   issue has a set number of "Bounty Points" assigned to it, which a student can
   claim once the issue has been resolved
4. Students pick a repository, fork it, clone it to their machines, use proper
   git branching and commit convention to make their changes to resolve the
   issue. They'll then open a pull request with their change.
5. A maintainer for the repository reviews their change. There's a feedback loop
   introduced here, where changes can be made to the open PR based on maintainer
   feedback.
6. If everything goes well, the branch on the fork gets merged into the main
   repository!
7. Finally, the student gets assigned the bounty points for that specific issue.

There's an added element of competitiveness when you introduce a points system,
to incentivize students to try and learn new languages (albeit for the sake of
getting points), or also to encourage them to contribute to a wider number or
range of repositories, even if it's a domain they're unfamiliar with.

As a maintainer, and as someone who wishes to see the developer ecosystem in
their university grow, there's nothing better than being in a place to encourage
people to experiment with multiple languages or technologies in a safe way!

## What's BunSamosa Bot?

To answer this, we must first answer the question "What is a Bun Samosa?" - The
Bun Samosa is a college legend on my campus. It's a Samosa sandwiched in a Bun,
and it's just about the best thing you can eat.

![TODO Bun Samosa Image]() _Bun Samosa with Coffee!_

Now that we know what a Bun Samosa is, we're set to start our journey.

The core premise of what makes Hacknight such a fun activity, apart from the
learning, is the sense of competitiveness that we encourage through the Bounty
System.

Throughout the event, there's a live leaderboard that keeps track of which
contributor has the most Bounty Points. (The real Bounty is the learning along
the way, but I hope this is something people realise :). However, having this
leaderboard implies that there's some sort of entity that's keeping track of
contributions and bounty points being assigned, and that's where the BunSamosa
Bot comes into picture.

## Technically Speaking..

BunSamosa Bot, put formally, is the nerve center of all bounty related
information in Hacknight. It's existed under a multitude of names in the past -
ACM-bot, chick-bot, and so on.

At a technical level, BunSamosa Bot is a Golang web service that acts as both a
Github Bot (to accept contributions and Assign Bounty) and an API (to serve the
leaderboard information).

A Github Bot is essentially a REST API that listens to POST Requests from Github
(fired as WebHooks). Given we already had this architure in place, it was fairly
trivial to add another endpoint to serve the data from the leaderboard here.

![TODO Github Bot general Arch Diagram]() _This is what just about every github
bot looks like under the hood_

## Getting our Hands Dirty

Lovely! So now we know what we're designing for. We want to be able to

1. Handle POST Requests coming in from Github
2. Be able to take actions **On Github** based on these POST Requests
3. Store contributor Bounty Data
4. Serve the Leaderboard

### Why Golang?

I decided to use Golang for this project given that I hadn't worked a lot with
it before, and this seemed like a fun way to explore the language a little
more - given Golang's position as the language of choice for writing web backend
services, this seemed like a nice starting line.

### Interfacing with Github

To our fortune, the awesome Golang Community happens to have a bunch of
implementations for Github Bots that we could feel free to draw inspiration
from. We found [this: TODO]() implementation and started off by using that as
our base.

To be able to talk to Github in a secure manner, we needed to

1. Host our Bot on the cloud
2. Have SSL Configured for security

We'll talk more about all of these factors in a while.

Interfacing with Github involved using
[Google's Github API Library](https://github.com/google/go-github) as well as
[ghinstallation](https://github.com/bradleyfalzon/ghinstallation) for
Authentication. Both of these needed secret keys that I kept in a private
`secrets.yaml` file.

Later on, I was able to add a "dev mode" to swap out the secrets file being used
based on an environment variable. This was a DX win (mostly for myself).

### Data Architecture

### 

- Include the graphs
- What I built, arch diagram
- Data Access
- Special note on the SQL needed
  - Also the messed up query, optimizations to be made on transaction-based
    query + the diagram
- Link to Github
- bugs faced

# Rewrite

- What's BunSamosa Bot
  - Usecases
  - Access Patterns
  - Responsibilities
    - Compute Bounty
    - Compute Leaderboard
    - Ingest Bounty events
    - Reply to newly-opened PRs
- High-level Dataflow Diagram
  - Interface with Github - the Webhook interface
  - How does it take actions if the webhook only pushes? (Github API)
  - Parsing the comment
    - Github treats issues and PRs as the same thing on the backend!
- Application Architecture
  - App and DBManager pattern
  - `net/http` is all you need
  - Had to use an ORM :(
- Deployment
  - Graphs!
  - SSL Config
  - Running it as a service with systemctl
  - Resource-Efficiency ($6/mo Droplet, 1GB RAM Only)
    - What are the spikes?
- Database Design
  - Current Approach - time-series log only
    - Messy Recomputation (but SQLite still holds up!)
  - Proposed Approach - Atomically-updated materialized views
- Future Developments
