---
layout: post
title: Handy Git tips to stop you getting fired
author: Phil Jackson
description: |

  Below I share some git aliases and tips for working with any sized
  repos.

---

## File level ignoring

Sometimes you find you want to change a file that's in a repository
but by no means want to check in your edits. This in itself is a
"smell" and you should possibly explore the issue but as we all know
in the real world sometimes php developers commit configuration files
and things that are used in production where they shouldn't be.

Say you want to edit `passwords.php` and for god's sake not check it
in to `develop` since you got that horrible telling-off for running
tests over the production database, you need to be really
careful. `.gitignore` won't do the trick because that's committed too,
so then you risk someone forgetting about their valid edits of the
file later. Here's a trick instead:

    assume   = update-index --assume-unchanged
    unassume = update-index --no-assume-unchanged
    assumed  = "!git ls-files -v | grep ^h | cut -c 3-"

The flow being:

    $ git status

      # modified passwords.php
      # modified lib/user.php

    $ git assume passwords.php
    $ git status

      # modified lib/user.php

    $ git assumed

      passwords.php

No chance of checking in `passwords.php` now. You're safe from the
wrath of the CTO for a bit.

    $ git unassume passwords.php
    $ git status

      # modified passwords.php
      # modified lib/user.php

## Merging theirs, ours, anyone's

Often during a merge you know you want to take a file from one side
wholesale. The following aliases expose the `ours` and `theirs`
commands which will let you pick a file(s) from the current branch or
the merged branch respectively:

    ours   = "!f() { git commit --ours $@ && git add $@; }; f"
    theirs = "!f() { git commit --theirs $@ && git add $@; }; f"
