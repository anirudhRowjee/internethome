---
title: Saaru - Rust India May 2023 Meetup
date: 2023-05-27
description: this is about Saaru, a Static Site Generator
collections:
  - tech
tags:
  - featured
  - WIP
  - rust
  - tech
---

## `$ whoami`

Hello, I'm Anirudh!

- Backend and Distributed Systems Dev
- Databases Enthusiast (I call feb 14th WALentine's day)
- 3.99999th Year CSE @ PES
- Lab Head, PES Innovation Lab
- Rust, Golang
- accidental poet

## What is Saaru?

- Saaru is a Static Site Generator -> In simple words, it takes in Markdown files, a bunch of templates, and spits out a whole website.

- Why is it called Saaru?
  - Saaru is the Kannada word for rasam and I love rasam

### Architecture

```text
                         STATIC SITE GENERATOR INTERNALS

      ┌────────────────┐
      │ Markdown Files │--------\
      └────────────────┘        |
                                v
      ┌────────────────┐     ┌─────────────┐    ┌──────────────────┐
      │ Template Rules │---->│ Static Site │--->│ Full Built HTML  │
      └────────────────┘     │ Generator   │    │ and CSS Website  │
                             └─────────────┘    └──────────────────┘
      ┌────────────────┐        ^
      │ Additional     │--------|
      │ Metadata       │
      └────────────────┘

```

- The content lives as `.md` files enriched with _frontmatter_, which is YAML, TOML or JSON that you can add at the top of a markdown file.

- You combine these files with a template, which can literally be a string with special blocks to substitute text in, or a more specialized dialect of templating like Jinja, Nunjucks, etc

- Saaru is built on combining a Markdown Engine, a Templating Engine, and Content Injection

```text


                      MARKDOWN ENGINE

         ┌────────┐      ┌────────┐        ┌─────────────┐
         │.md file│----->│Markdown│------->│compiled HTML│
         └────────┘      │Engine  │        │output       │
                         └────────┘        └─────────────┘

```

- Saaru is a static site generator in Rust
  - (show docs folder) Give it a folder with Markdown Content and Some Templates, Saaru will render a website (show `build` folder)
  - Deep Data Merge: You can have content show up in indices like tags and collections, so that you can reference them in other parts of the site

* Switches from a one-pass traversal of the dir tree to a two-pass approach (or does it? :sus:)

  - The frontmatter
  - Controls the title, a bunch of other stuff - here's what the frontmatter looks like

* Frontmatter is metadata you can control. Here's what this looks like -> Each `.md` file has this, and this can control how it gets rendered.

```rust
#[derive(Clone, Serialize, Deserialize, Debug)]
pub struct FrontMatter {
	pub title: Option<String>,
	pub description: Option<String>,
	pub date: Option<String>,
	pub tags: Option<Vec<String>>,
	pub collections: Option<Vec<String>>,
	pub wip: Option<bool>,
	pub template: Option<String>,
	pub link: Option<String>,
	pub meta: Option<Value>,
}
```

## Live Demo (with my website)

- Here's a working Saaru website. See the structure, and the frontmatter.

## commit walkthrough from the bottom up

- Frontmatter struct represents a file, content, tags and all

### talk about how initial R&D Happened

- Genesis commit - (https://github.com/anirudhRowjee/saaru/commit/974009349027a4f14a69959b8a1e87c74dcad0b4)

  - Goal: Render a single file of markdown using a template engine
  - was still using `pulldown-cmark` at that time
  - no focus on dynamic naming
  - `include_str!()` to read the file!

- Attempting to remove the frontmatter myself

  - learnt about `.map()` and `.fold()` used together (thanks Kiran!)
  - Added a minimal test suite
  - Led me to think "damn, i really need to reorganize this codebase" (https://github.com/anirudhRowjee/saaru/commit/bd03832a571c77cd32a2ec14627f94c42a75141f)

- Load the template name dynamically based on frontmatter

  - Learnt more about minijina environments and how they can pick up templates automatically based on directory
  - coming up with ONE function to render a single markdown file based on its templates

- How easy it was to move from the system-native directory walker (`fs::read-dir` to the `WalkDir::new` walker (which is recursive)

  - Most satisfying refactor of my life -> single-line change and required _nothing_ else to be touched
  - https://github.com/anirudhRowjee/saaru/commit/c1553cad881013eef1d8aa2151f9488265ffaec4

- Early Deep Data Merge (https://github.com/anirudhRowjee/saaru/commit/b2185239455ec3386ba2f503409281a0ad066f69)

  - Conversion from a one-pass operation to a two-pass operation (introducing `alternate_render_pipeline()`)
  - DO NOT WALK THE FS TWICE -> INDIRECT TO `tag_map`, `collection_map`, `frontmatter_map`
  - Read the FS into these maps, and iterate over `frontmatter_map` to render
  - These maps can pull from `tag_map` and `collection_map` to render more content based on tags and collections
  - Added proper structured logging here

- Deep Data merge (https://github.com/anirudhRowjee/saaru/commit/badcac9b06f44731c3a8cac5abf00cdf21914f10)

  - More refactoring! refactor all the things!
  - moved most stuff from `src/main.rs` to `src/saaru.rs`
  - premanently switched to two-pass render pipeline (`instance.recursively_render_from_directory()` -> `instance.alternate_render_pipeline()`)
  - Load data into the `tag_map`, pass it to template rendering context
  - move to `thin_augmented_frontmatter` -> frontmatter without the raw markdown content
  - Lastly added collections (alternate ontology to tags) and a system-generated default tags page

- markdown engine switch

  - Added Command-line Arguments (https://github.com/anirudhRowjee/saaru/commit/feab2d7f2224bcd920ef95d1bd774c5c18b01cf6)

- Switched from `pulldown-cmark` to `comrak` (https://github.com/anirudhRowjee/saaru/commit/2d60a103e72e6e38d5460ba77ef1aeca212a9c1f)
  - Why?
    - more features -> Footnotes, etc
    - Better compatibility with common requests to deviate from GFM Spec
    - more configurability and much simpler API, although less suitable for user-injected AST-level customizations
    - Automatically ignores frontmatter!

### adding other features

- Static folder copying for CSS, etc (`src/utils.rs` -> `pub fn copy_recursively`, shamelessly stolen from the internet)
  - Basic JSON content injection
  - Skipping non-markdown files in the parse tree
  - Yet Another Refactor!

### live reload

- Added basic live reload with `notify` -> loop {poll_fs, if file changes, rerender that file in-place with `instance.render_individual_file(filepath)`} (https://github.com/anirudhRowjee/saaru/commit/c612455d70d911897742456dd0951c8b91dbcb1c)

  - had to manually refresh browser and external web server!
  - Initially architectured it as a separate module that consumed the `saaru` instance
  - Re-architected when adding a live server (https://github.com/anirudhRowjee/saaru/commit/b5f96fff134ab1db656872de8e19a7acb196becb)
  - New Fancy Diagram!
  - crossbeam MPMC channels, watching for fs change, waiting to notify Saaru Instance as well as web server (for live reload) -> `notify`, `tower`, `tower-http-livereload`

- 2 lightweight threads (watcher thread, message passing thread)
  - Hid these configurations behind command line flags
  - Specific actions for different types of files
  - `.md` -> Optimized re-render of single file only
  - `.css` -> separate watcher thread for static files, copy that specific file over into the build folder if there's a change

## future plans

- DAG solver -> more fine-grained re-rendering to check what templates etc need to be re-loaded
- maybe add template livereload live while i have time
