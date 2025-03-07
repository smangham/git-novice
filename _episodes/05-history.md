---
title: "Exploring History"
slug: git-novice-exploring-history
teaching: 10
exercises: 0
questions:
- "How can I review my changes?"
- "How can I recover old versions of files?"
objectives:
- "Identify and use Git revision numbers."
- "Compare files with previous versions of themselves."
- "Restore old versions of files."
keypoints:
- "`git diff` displays differences between commits."
- "`git checkout` recovers old versions of files."
---

## Exploring History

We've seen that `git log` gives us some information on what commits were made when, but let's look a bit deeper at the specifics:

~~~
$ git log
~~~
{: .language-bash}

~~~
commit f15ad111042cee7492f40ad6ff0ec18588fce753 (HEAD -> main)
Author: Sam Mangham <mangham@gmail.com>
Date:   Wed Mar 30 17:15:47 2022 +0100

    Add rainfall processing placeholder

commit 6aeaf44173344939e9994d7ccb5512fc5b26c211
Author: Sam Mangham <mangham@gmail.com>
Date:   Wed Mar 30 17:14:14 2022 +0100

    Add Docstring

commit 503f02b5f51d5622121e204494dfabc9b2ae7410
Author: Sam Mangham <mangham@gmail.com>
Date:   Wed Mar 30 17:12:02 2022 +0100

    Added a basic readme file

commit 499b6d18b36a25d3f5ab9be1b708ea48fef1dd65 (origin/main, origin/HEAD)
Author: Sam Mangham <mangham@gmail.com>
Date:   Wed Mar 16 14:19:13 2022 +0000

    Initial commit
~~~
{: .output}

We can see commits identified by long IDs, but also **HEAD** at the top of the log. **HEAD** is the name used to refer to the **most recent** end of the chain of commits to our **local repository**.

### Relative History

What if somehow we've introduced a bug, and we want to see what's changed between our latest version of the code and the copy that was working last commit, or a few commits ago? Which lines did we edit, and what did we add?

We can use `git diff` again to look for differences between files, but refer to the versions of the files **as saved in older commits** using the notation `HEAD~1`, `HEAD~2`, and so on to refer to the commits.
We can refer to previous commits using the `~` notation,
so `HEAD~1` (pronounced "head minus one")
means "the previous commit",
while `HEAD~123` goes back 123 commits from the latest one.

~~~
$ git diff HEAD~1 climate_analysis.py
~~~
{: .language-bash}

~~~
diff --git a/climate_analysis.py b/climate_analysis.py
index d5b442d..c463f71 100644
--- a/climate_analysis.py
+++ b/climate_analysis.py
@@ -26,3 +26,5 @@ for line in climate_data:
             kelvin = temp_conversion.fahr_to_kelvin(fahr)

             print(str(celsius)+", "+str(kelvin))
+
+# TODO: Add rainfall processing code
~~~
{: .output}

So we see the difference between the file as it is now, and as it was **the commit before before the latest one**.

~~~
$ git diff HEAD~2 climate_analysis.py
~~~
{: .language-bash}

~~~
diff --git a/climate_analysis.py b/climate_analysis.py
index 277d6c7..c463f71 100644
--- a/climate_analysis.py
+++ b/climate_analysis.py
@@ -1,3 +1,4 @@
+""" Climate Analysis Tools """
 import sys
 import temp_conversion
 import signal
@@ -25,3 +26,5 @@ for line in climate_data:
             kelvin = temp_conversion.fahr_to_kelvin(fahr)

             print(str(celsius)+", "+str(kelvin))
+
+# TODO: Add rainfall processing code
~~~
{: .output}

And here we see the state **before the last two commits**, HEAD minus 2.

### Absolute History

What about if we want to compare our version of the code to the version from last month, or from the version we used to make a paper last year? 
Calculating the number of commits just isn't realistic - it'll change all the time as we keep working on the code.
Instead, we can refer to **specific revisions** using those long strings of digits and letters that `git log` displays.

These are unique IDs for the changes,
and "unique" really does mean unique:
every change to any set of files on any machine
has a unique 40-character identifier. (A SHA-1 hash of the new, post-commit state of the repository).

If we scroll down to the bottom of the `git log` output, we can see the ID for our first commit.
In the example above, it's `499b6d18b36a25d3f5ab9be1b708ea48fef1dd65` (but **yours will be different!**).
However, 40 characters just isn't very practical to type out, 
so you can use however much of an ID you need to pick out a single, unique commit.
By default, Git suggests you use the first **seven** characters.

{: .challenge}
> ## Exploring Absolute History
>
> Using unique commit IDs, get a summary of all the changes you've made to the `climate_analysis.py` file since the initial commit.
> 
> {: .solution}
> > ## Solution
> >
> > We can use `git log` to get a list of all of our commits we've made:
> > ~~~
> > $ git log
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > [snipped for space]
> > commit 499b6d18b36a25d3f5ab9be1b708ea48fef1dd65 (origin/main, origin/HEAD)
> > Author: Sam Mangham <mangham@gmail.com>
> > Date:   Wed Mar 16 14:19:13 2022 +0000
> > 
> >     Initial commit
> > ~~~
> > {: .output}
> > 
> > Then, we can take the first 7 characters of the commit ID of the initial commit,
> > and use them with `git diff`:
> >
> > ~~~
> > $ git diff 499b6d1 climate_analysis.py
> > ~~~
> > {: .language-bash}
> >
> > 
> > ~~~
> > diff --git a/climate_analysis.py b/climate_analysis.py
> > index 277d6c7..6f8ed8a 100644
> > --- a/climate_analysis.py
> > +++ b/climate_analysis.py
> > @@ -1,3 +1,4 @@
> > +""" Climate Analysis Tools """
> > import sys
> > import temp_conversion
> > import signal
> > @@ -25,3 +26,5 @@ for line in climate_data:
> >              kelvin = temp_conversion.fahr_to_kelvin(fahr)
> >  
> >              print(str(celsius)+", "+str(kelvin))
> > +
> > +# TODO: Add rainfall processing code
> > ~~~
> > {: .output}
> >

Being able to reference specific commits absolutely is particularly handy, 
as it lets you **exactly identify specific versions of the code**.
For example, you can identify the version of the code you used to write your first paper, 
and the different, newer version you used to write your second paper.

![Differencing]({{ site.url }}{{ site.baseurl }}/fig/05-history/diff.svg){:width="60%"}

> ## Other Ways To Reference Commits
>
> Newer versions of Git have some more advanced ways of referencing past commits. In place of `HEAD~1` you can use `HEAD~` or `HEAD@{1}`,
> or you can even use text to ask more advanced questions, like `git diff HEAD@{"yesterday"}` or `git diff HEAD@{"3 months ago"}`!
>
> You can also 'tag' a commit with a name, using `git tag` to create tags with IDs and descriptions. If you've got a version of the code you've used in a paper, for example, tagging it is a good idea:
>
> ~~~
> $ git tag -a v1.0 -m "Version 1.0, used in paper Mangham2024."
> ~~~
> {: .language-bash}
>
{: .callout}

### Restoring Files

All right:
we can **save changes** to files and **see what we've changed** &mdash; suppose we need to **restore** older versions of things?

Let's suppose we **accidentally** overwrite or delete our file:

~~~
$ rm climate_analysis.py
$ ls
~~~
{: .language-bash}

~~~
README.md
temp_conversion.py
~~~
{: .output}

**Whoops!**

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

~~~
$ git status
~~~
{: .language-bash}

~~~
# On branch main
# Your branch is ahead of 'origin/main' by 3 commits.
#   (use "git push" to publish your local commits)
#
# Changes not staged for commit:
#   (use "git add/rm <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#
#	deleted:    climate_analysis.py
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

Following the helpful hint in that output, we can put things back the way they were
by using `git restore`:

~~~
$ git restore climate_analysis.py
$ cat climate_analysis.py
~~~
{: .language-bash}

~~~
[SNIPPED - but changes rolled back]
~~~
{: .output}

By default, `restore` replaces the file with the version of it in the *staging area*. If you haven't used `git add`, that should be the same as the version in the last commit. But what if we already used `git add` on our incorrect version of a file, or we broke the file more than one commit ago?

We can use `git checkout`, e.g.:
~~~
$ git checkout <HEAD or commit ID> climate_analysis.py
~~~
{: .language-bash}


{: .callout}
> ## Compatibility Notice
>
> Older versions of Git don't include the `git restore` command - fortunately, it's just a shortcut for `git checkout --`. If `git restore` doesn't work, try `git checkout -- temp_conversion.py`. `checkout` has a *lot* of functions, and newer versions of Git simplify things by giving them new names.


{: .caution}
> ## Double Whoops
> What if you accidentally did `git rm climate_analysis.py`? 
> That command tells Git to *delete the file and remove it from the repository* - so it will record that the file has been deleted, then stop tracking further changes. 
> Even if you re-make the file, it won't be tracked until you use `git add` on it again.
>
> The file still exists in the *history*, though so if you want to undo this you can do `git checkout HEAD climate_analysis.py`, to get the file back and start tracking it again. 
> Since you can retrieve any file that existed in *a* previous commit, even if you removed it from future ones, 
> this makes it important **not to commit files containing passwords or sensitive information!**
> To avoid this, you can use a [.gitignore file](../_episodes/08-ignore/index.html) to prevent you adding sensitive files in the first place. 
> You *can* fully delete files from a repository's history with tools like the [BFG](https://rtyley.github.io/bfg-repo-cleaner/) but it can be high risk.


![Restoring Files]({{ site.url }}{{ site.baseurl }}/fig/05-history/restore.svg){:width="60%"}

The fact that files can be reverted one by one tends to change the way people organize their work.

Consider a situation where all your code is in one file, 
and you fixed a bug in one section but accidentally introduced one elsewhere. 

You can't just roll back to fix one bug without un-fixing the other. 
However, if each section is in its own file, you can just roll back the section you broke!
