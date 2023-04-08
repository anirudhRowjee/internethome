---
title: Building A Leaderboard in React
date: 2021-10-14
description: I Describe how I built a Leaderboard for ACM PESUECC's Hacknight 3.0
tags:
  - builds
  - hacknight
  - ACM
  - WIP
draft: false
---

The primary objective of this post is to elaborate on how simple it is to build seemingly complex ReactJS applications, and how you can extensively use React's modular architecture to your advantage to make life a little easier.

For example, take this leaderboard -

![HackNight 3.0 Leaderboard](/static/images/leaderboard/leaderboard_main.png)

**DISCLAIMER**

- **You can check out the leaderboard in question [here](https://hacknight3-leaderboard.netlify.app/)**. It's no longer live - since the event's over, all the data's been moved to a static file. So long, backend!
- The Code can be found [here](https://github.com/acmpesuecc/HackNight-LeaderBoard/tree/1e13cdb71a45ebebfa221284682e0bd886c18996). I've referenced the leaderboard at a particular commit since Hacknight opened the leaderboard itself to contributions, post which some features were added. What you see right now isn't the same commit referenced.

## Why a Leaderboard?

HackNight is ACM PESUECC's Annual Celebration of Hacktoberfest, which involves conducting [workshops](https://www.youtube.com/watch?v=raQrUlURXEc) and setting up beginner-level repositories to help students get started contributing to open-source software.

As part of this, a student is awarded **bounty points** for every issue successfully resolved (a la Bug Bounties) within a span of 24 hours, and the contributor with the most bounty points at the end wins a bunch of prizes!

As the Technical Lead for the event, it was my responsilbility to setup the Bounty Tracking Infrastructure (by means of a Github Bot, affectionately known as **Chick-Bot** - there's a blog post on this coming soon), as well as the **Leaderboard**, which helped tally the scores and display the names of the contributors in order of their bounty points.

## App Architecture

Most dynamic front-end apps separate themselves into layers, where each concern can be handled well. Usually, you'll have -

1. Data Fetching Layer (using `axios` or `fetch`) to get data from the Backend
2. Interactivity Layer (using Hooks such as `useEffect`) to handle user interactions with the application
3. Presentation Layer (good ol' HTML and CSS)

Here's the code for the entire leaderboard -

```javascript
import React, { useEffect, useState } from 'react'
import './table.css'
import axios from 'axios'

const endpoint = 'https://acm.savaal.xyz/leaderboard'

const get_leaderboard_data = () => {
  return new Promise((resolve, reject) => {
    axios
      .get(endpoint)
      .then((response) => {
        resolve(response.data)
      })
      .catch((error) => {
        reject(error)
      })
  })
}

const Scores = (props) => {
  const [scores, setScores] = useState()

  useEffect(() => {
    get_leaderboard_data()
      .then((data) => {
        // sanitize the scores
        var player_score_object = {}
        data.map((score) => {
          if (!player_score_object.hasOwnProperty(score.contributor)) {
            player_score_object[score.contributor] = 0
          }
          var clean_score = parseInt(score.points)
          player_score_object[score.contributor] += clean_score
          return null
        })

        // load into a list and sort
        var scores_array = []
        for (var key in player_score_object) {
          scores_array.push({
            contributor: key,
            score: player_score_object[key],
          })
        }

        scores_array.sort((first, second) => first.score < second.score)
        setScores(scores_array)
      })
      .catch((err) => {
        console.error(err)
      })
  }, [])

  return (
    <table align="center">
      <thead>
        <tr>
          <th> Position </th>
          <th> Contributor </th>
          <th> Bounty </th>
        </tr>
      </thead>
      <tbody>
        {scores &&
          scores.map((score, index) => {
            return (
              <tr key={score.contributor}>
                <td> {index + 1} </td>
                <td> {score.contributor} </td>
                <td> {score.score} </td>
              </tr>
            )
          })}
      </tbody>
    </table>
  )
}
export default Scores
```

That's it. That's literally the entire leaderboard! Don't worry if it's a fair bit to take in at once. You'll be able to understand it all in no time!

You can see that the this exports a single functional component, which is all we need. Let's walk through it, layer by layer.

## The Data-Fetching Layer

Here, we use axios, a widely-used javascript library, to fetch the data from our backend, which is a REST API.

On the backend, we stored every event as its own record in MongoDB, which is why our API response looked something like this -

```json
[
  // more data here
  {
    "_id": "6168b248330de71636aaa3fa",
    "html_url": "https://github.com/acmpesuecc/bigboard/issues/6",
    "contributor": "anirudhRowjee",
    "issue_number": 6,
    "maintainer": "anirudhRowjee",
    "points": "100",
    "repository": "acmpesuecc/bigboard",
    "timestamp": "2021-10-14T23:03:34Z"
  }
  // more data here
]
```

This decision was made to ensure we had granular control over the scores in the event of any foul play. There's also the added benefit of the data serving as a time-series log of the entire event, so in the interest of these factors, we chose not to pre-process the data on the backend.

On the React side, the API call to our backend gave us an array of the above JSON structure.

```javascript
import axios from 'axios'

const endpoint = 'https://acm.savaal.xyz/leaderboard'

const get_leaderboard_data = () => {
  return new Promise((resolve, reject) => {
    axios
      .get(endpoint)
      .then((response) => {
        resolve(response.data)
      })
      .catch((error) => {
        reject(error)
      })
  })
}
```

Here, we're declaring a function called `get_leaderboard_data` which makes an `HTTP GET` request to the above endpoint.

To increase modularity, and ensure this function can be efficiently reused in other code, I wrapped it in a `Promise` to ensure it would be `async-await` compliant. You'll see this in use once we explore the interaction layer.

In essence, a `Promise` is "an object representing the eventual completion or failure of an asynchronous operation", where the operation in question can be anything from an API Call to a really slow function. We do this to ensure programs don't freeze when they must wait for something to finish, and can continue functioning otherwise.

Promises can have one of three states -

- Pending - This means that the calling function must wait for a value
- Fulfilled - The value was returned successfully
- Rejected - There was an error, and the value could not be returned

The above function, in essence, calls `axios.get()`
