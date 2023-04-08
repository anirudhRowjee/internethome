---
title: The Warp Web Framework
date: 2020-07-19
description: Learn about the Warp Web Framework in Rust
tags:
  - rust
  - WIP
  - warp
  - HTTP
---

* The Warp Web Framework #rust #warp

  * This is a web framework written in Rust, built on top of `Tokio` and `hyper`.
  * https://docs.rs/warp/latest/warp/, reference video for this \* https://www.youtube.com/watch?v=HNnbIW2Kzbc
  * It uses the standard Tokio async runtime
  * ## Hello World Snippet

    ```rust
    		  use warp::Filter;

    		  #[tokio::main]
    		  async fn main() {

    		      let hello_world = warp::path::end()
    		          .and(warp::get())
    		          .map(|| "hello world from root!");

    		      let hi = warp::path("hi")
    		          .and(warp::get())
    		          .map(|| "Hello from Hi!");

    		      // combine the two filters
    		      let routes = hello_world.or(hi);

    		      println!("starting the web server...");
    		      warp::serve(routes).run(([127, 0, 0, 1], 8000)).await;
    		  }
    ```

  * Filters

    * The core idea behind Warp's composable architecture is the concept of a Filter (`warp::Filter`). Filters are inherently composable, and can be used to define
      * Collections of Routes
        * `warp::path("<path here>")` to signify the path segment to match
        * `warp::path::end()` to signify that the path is over \* Not using this will allow for first prefix match wildcard routing
      * Pattern\*matching Mechanisms
        * HTTP headers and Methods
          * See `.and(warp::get())` \* without this, all HTTP Methods will give you the same response on the route.
            * Once these method filters are used, you get an _HTTP 405 Method Not Allowed_ response on an unspecified method.
    * Multiple Filters can be combined to add more routes/functionality to the current route being served. See `let routes = hello_world.or(hi)`.
      * This combines the `hello_world` and the `hi` filter as one of many routes that the server can serve.
      * for path filters \*> **The order in which filters are called are also the order in which they're applied on the request.**
    * ## Static files can also be served as a filter.

      ```rust
      			  const WEB_FOLDER: &str = "static/";
      			  #[tokio:main]
      			  async fn main() {
      			    ...
      			    let staticfiles = warp::fs::dir(WEB_FOLDER);

      			    let routes = hello_world.or(hi).or(staticfiles)
      			    ...
      			  }
      ```

      * This takes care of making sure the folder is there, etc

  * Implementing your own Filter

    * A Filter can look like this, to be imported from another file \*

      ```rust
      		  use warp::Filter;

      		  // implement a custom filter or set of methods for the URL API Methods

      		  pub fn todos_filter() *> impl Filter<Extract = impl warp::Reply, Error = warp::Rejection> + Clone {
      		      warp::path("urls")
      		          .and(warp::get())
      		          .and(warp::path::end())
      		          .map(|| "get all URLs!")
      		  }
      ```

    * DONE Figure out why that type signature is required
      * Filters have two primary reasons they need to be so composable \* to filter, and to augment. Thus, a filter needs to implement an `Extract` trait, which tells every other filter _what it takes out from previous filters_ and _what it gives back_, and the `Error` trait tells the type system what will be returned in the event of a failure.
      * In this case, the signature roughly says \*
        * TODO this
    * To pass an Async function into the closure (as we often need to do for database calls), and because the async closure is unstable, we can pass a closure with an async block into `.and_then()`.
      * Primitively,
        ```rust
        			      warp::path("urls")
        			          .and(warp::get())
        			          .and(warp::path::end())
        			          .and_then(|| async { Ok::<&str, warp::Rejection>("Will get all URLs") })
        ```
      * The turbofish is necessary as it is a closure. Regular functions are fully typed, and hence, don't need to ask the user for any help inferring their return types.
      * It's trivial to refactor that into a function \*
        ```rust
        			  async fn urls_list() *> Result<String, warp::Rejection> {
        			      Ok("Listing all URLs in async".to_string())
        			  }
        			  // in the handler
        			          .and_then(urls_list)
        ```
        And this now can hold whatever connection logic you wish to pass it.
    * Route Pattern

      * A simple pattern is to organize your routes around a base route, and use the `or` composition to create routing.
      * ```rust
        			  pub fn todos_filter() *> impl Filter<Extract = impl warp::Reply, Error = warp::Rejection> + Clone {

        			    	// base route defined
        			      let urls_base = warp::path("urls");

        			      let list = urls_base
        			          .and(warp::get())
        			          .and(warp::path::end())
        			          .and_then(urls_list);

        			      let single_list = urls_base
        			          .and(warp::get())
        			          .and(warp::path::end())
        			          .and_then(urls_list);

        			      list.or(single_list) // composition used
        			  }
        ```

    * JSON for Replies

      * We can use `serde_json` along with `warp::Reply::Json` to return JSON from a function.
      * Fetch JSON _> Convert to JSON with `warp::reply::json()` _> return as `Ok()`
      * ```rust
        			  async fn urls_list() *> Result<Json, warp::Rejection> {

        			      // TODO get from DB
        			      let todos = json!([
        			          {"id": "1", "url": "abc", "shortcode": "def"},
        			          {"id": "2", "url": "wxy", "shortcode": "xyz"},
        			      ]);

        			      let urls_json = warp::reply::json(&todos);
        			      Ok(urls_json)
        			  }
        ```

    * Adding a path parameter
      * we can use the `warp::path::param()` with an `and` to add a path parameter at some point.
      * ```rust
        			      let single_list = urls_base
        			          .and(warp::get())
        			          // add a path parameter * This type is inferred based on the first argument that the
        			          // handler function takes.
        			          // Parse Failures are 404s.
        			          .and(warp::path::param())
        			          .and(warp::path::end())
        			          .and_then(urls_single_list);
        ```
      * The type of the parameter is inferred and parsed based on the argument of the handler function. 😍
    * ## Reading request body

      ```rust
      			  	// in the route declaration phase
      			  	let create_url = urls_base
      			          .and(warp::post())
      			          .and(warp::body::json())
      			          .and_then(urls_create);

      			  // handler function
      			  async fn urls_create(req_body: Value) *> Result<Json, warp::Rejection> {
      			      // TODO add a new URL to the Database.
      			      let url_new = req_body;
      			      let url_json = warp::reply::json(&url_new);
      			      Ok(url_json)
      			  }
      ```

  * ## Auth as a custom filter

    ```rust
    		  use warp::Filter;

    		  const HEADER_XAUTH: &str = "X*Auth*Token";

    		  pub fn check_auth() *> impl Filter<Extract = ((),), Error = warp::Rejection> + Clone {
    		      // implement custom auth to check the header to see if we're authenticated our not

    		      // implement a blank filter
    		      warp::any()
    		          .and(warp::header::<String>(HEADER_XAUTH))
    		          .and_then(|xauth: String| async move {
    		              // trivial auth check
    		              if !xauth.ends_with(".exp.signature") {
    		                  return Err(warp::reject::custom(FailAuth));
    		              }

    		              Ok::<(), warp::Rejection>(())

    		          })
    		  }

    		  // this is a custom error type, better to use this
    		  // than to panic or do something similar
    		  #[derive(Debug)]
    		  pub struct FailAuth;
    		  impl warp::reject::Reject for FailAuth {}

    ```

    * `warp::any` can be thought of as a blank filter that implements nothing

  * Auth as a custom Filter \* Returning the User Context
  * Sharing State with Filters
    * Shared State (Database Pool, etc) can be implemented as a custom filter that extracts nothing, but passes along a new, reference\*counted version of a method to access shared state into the handler function parameters.
