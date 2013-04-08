---
layout: post
title: Handy Git tips to stop you getting fired
author: Phil Jackson
description: |

  Below are some tips which might just stop you making that one
  horrible mistake that makes your CTO wonder if your probationary
  period is over.

---

## File level ignoring

Sometimes you find you want to change a file that's in a repository
but by no means want to check in your edits. This in itself is a
"smell" and you should possibly explore the issue with a view to
fixing it properly, but, as we all know, in the real world sometimes
php developers commit configuration files and things that are used in
production where they shouldn't be.

Say you want to edit `passwords.php` and for god's sake not check it
in to `develop`. `.gitignore` won't do the trick because that's
committed too, so then you risk someone forgetting about their valid
edits of the file later. Here's a trick instead:

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
wrath of HR for a bit.

    $ git unassume passwords.php
    $ git status

      # modified passwords.php
      # modified lib/user.php

## Use snapshot stashes

Take a snapshot of your current working tree without removing the
changes from your tree. This is handy for refactoring where you can't
quite fit what you've done into a commit but daren't stray too far
from now without a backup.

    snapshot = !git stash save "snapshot: $(date)" && git stash apply "stash@{0}"

Creates this stash:

    stash@{0}: On feature/handy-git-tricks: snapshot: Mon Apr 8 12:39:06 BST 2013

And seemingly no changes to your working tree.

## Easy merging = fewer mistakes

### Them and us

Often during a merge you know you want to take a file from one side
wholesale. The following aliases expose the `ours` and `theirs`
commands which will let you pick a file(s) from the current branch or
the merged branch respectively:

    ours   = "!f() { git commit --ours $@ && git add $@; }; f"
    theirs = "!f() { git commit --theirs $@ && git add $@; }; f"

### Comparing large chunks of text

Using the patience diff algorithm to compare large swathes of
repetitive text (hello, xml!) often produces a much more readable diff
that will reveal context and therefore hopefully bugs/typos etc. The
description of the algorithm by the author is:

*Find all lines which occur exactly once on both sides, then do
longest common subsequence on those lines, matching them up.*

Here's a quick snippet of a diff to show it helping. Bare LCS-based
diff:

     def thing1 {
         something += 1
    +}
    +
    +def somethingelse {
    +    something += 1
     }

     def thing2 {
         something += 2
     }

With patience becomes:

     def thing1 {
         something += 1
     }

    +def somethingelse {
    +    something += 1
    +}
    +
     def thing2 {
         something += 2
     }

Same end result but much easier to read.

## Get everything you can into git hooks

Taking the time to setup your local hooks can be a godsend. If you're
strict testers then running the tests before a commit to
`master`/`develop` might help you out if you've forgotten to run them
post-merge. Also, running your linter and having it fail on errors
might stop you checking in code which makes your co-workers gradually
want to harm you.
