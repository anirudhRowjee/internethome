---
title: Using GNU Stow
date: 2024-06-30
description: the simplest guide to using GNU Stow
collections:
  - tutorials
  - FOSS
tags:
  - featured
  - computerscience
---

## What is Stow?

If you've been using tools that are configured via files, like i3, {neo,}vim, kitty, etc, then you know that getting your configuration file from place to place is a pain. Either you lug around a pen drive with the file, or you download it all the time, or you'll resort to something else like that.

Stow lets you bypass all of this by letting you commit your configuration files into version control, like Github. You can then clone just one repository and use git to synchronize it all the way through.

The core idea is that a file in your stow directory (source of truth) mirrors a file in your config directories via a symlink. Stow manages these symlinks, leading to people calling it a symlink farm.

## How do I use stow?

Ensure it's installed. This should be on your system package manager.

1. Create a folder where you'll like to store your configuration. Let's call this `~/dotfiles/`

2. Add the files you want to keep track of in the folder.

  note: The folder structure in your folder of choice _has to_ resemble the folder structure you want in the system. For example, if I want to keep track of a file ideally placed at `~/.config/nvim/init.lua`, the file needs to be placed at `~/dotfiles/.config/nvim/init.lua`

  You can also optionally add a "package". This is a top level folder in your stow source directory.

3. run `stow -n -v .` in `~/dotfiles/` to see what symlinks will be formed. Once you're comfortable with where your files are ending up, run `stow -v .` to make the changes happen.

## Making the changes go elsewhere

Git!

go to `~/dotfiles/` and do a `git init`. follow the usual git workflow to push this to github.

Once you're on a system where you want to replicate the config, git clone the repo you pushed, and run stow again in the same way (described in step 3) to make the files appear where you want them to.

### Sources
- https://systemcrafters.net/managing-your-dotfiles/using-gnu-stow/
- https://tamerlan.dev/how-i-manage-my-dotfiles-using-gnu-stow/
- https://apiumhub.com/tech-blog-barcelona/managing-dotfiles-with-stow/
