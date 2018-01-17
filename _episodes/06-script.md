---
title: "Shell Scripts"
teaching: 30 
exercises: 10
questions:
- "How can I save and re-use commands?"
objectives:
- "Write a shell script that runs a command or series of commands for a fixed set of files."
- "Run a shell script from the command line."
- "Write a shell script that operates on a set of files defined by the user on the command line."
keypoints:
- "Save commands in files (usually called shell scripts) for re-use."
- "`bash filename` runs the commands saved in a file."
- "`$@` refers to all of a shell script's command-line arguments."
- "Letting users decide what files to process is more flexible and more consistent with built-in Unix commands."
- "Use comments to explain the purpose of the script."
- "`#` symbol indicates the rest of the line is a comment."
---

We are finally ready to see what makes the shell such a powerful programming environment.
We are going to take the commands we repeat frequently and save them in files
so that we can re-run all those operations again later by typing a single command.
For historical reasons,
a bunch of commands saved in a file is usually called a **shell script**.

### Use `history` command to make shell script
We'll continue the Little Women example from the previous episode.
To recap, we typed a for loop which did something useful.
Let's save those commands for re-use in a shell script.
We'll use the `history` command to save the last couple of commands to a file
which we will run later.
It's convention to use the **.sh** file extension for shells scripts.
The shell doesn't care about file extensions --- but following convention makes
it easier for people to identify your shell scripts.

```
$ history | tail -n 2 > countwords.sh
$ cat countwords.sh
```
{: .bash}

```
1151  for name in Jo Meg Beth Amy; do  echo $name; grep $name littlewomen.txt | wc -w; done
1152  history | tail -n 2
```
{: .output}

> ## Text vs. Whatever
>
> We usually call programs like Microsoft Word or LibreOffice Writer "text
> editors", but we need to be a bit more careful when it comes to
> programming. By default, Microsoft Word uses `.docx` files to store not
> only text, but also formatting information about fonts, headings, and so
> on. This extra information isn't stored as characters, and doesn't mean
> anything to tools like `head`: they expect input files to contain
> nothing but the letters, digits, and punctuation on a standard computer
> keyboard. When editing programs, therefore, you must either use a plain
> text editor, or be careful to save files as plain text.
{: .callout}

After a small amount of editing to remove the serial numbers of the commands, and any commands
that aren't required, we will have a file which contains just the commands we want to re-run.
Remember, we are *not* running it as a command just yet:
we are putting the commands in a file.
This file also serves as a record of the analysis we previously ran.

Let's use the text editor `nano` to make those changes to our file.

```
$ nano countwords.sh
```
{: .bash}

```
for name in Jo Meg Beth Amy
	do  echo $name
	grep $name littlewomen.txt | wc -w
done
```
{: .output}

We need to press `Ctrl + O` to save, and then `Ctrl + X` to exit. These key combinations
are also given in the row at the bottom of the **nano** screen.
We are now ready to run our shell script, but first a brief detour to explain a detail of
the `history` command.

> ## Why Record Commands in the History Before Running Them?
>
> If you run the command:
>
> ~~~
> $ history | tail -n 5 > recent.sh
> ~~~
> {: .bash}
>
> the last command in the file is the `history` command itself, i.e.,
> the shell has added `history` to the command log before actually
> running it. In fact, the shell *always* adds commands to the log
> before running them. Why do you think it does this?
>
> > ## Solution
> > If a command causes something to crash or hang, it might be useful
> > to know what that command was, in order to investigate the problem.
> > Were the command only be recorded after running it, we would not
> > have a record of the last command run in the event of a crash.
> {: .solution}
{: .challenge}

### Running a shell script
Now we have saved the file,
we can ask the shell to execute the commands it contains.
Our shell is called `bash`, so we run the following command:

```
$ bash countwords.sh
```
{: .bash}

```
Jo
18877
Meg
8314
Beth
5684
Amy
7933
```
{: .output}

and we see the output is exactly the same as when we typed the commands directly into the shell.

### Adding comments
So now we have a record of the analysis we ran, but if anyone else wants to use the script,
they'll have to figure out what it does and how to run it.
In order to make our script more useful to other people (including our future selves),
let's add some text to describe the purpose of the script.
A **comment** is text which is ignored by the shell when the script runs.
We use the hash symbol (#) to mark text as a comment -- everything that follows a hash symbol
(until the end of the line) is ignored when the script is run.

Let's add a comment:

```
$ nano countwords.sh
# This script counts the number of times "Jo, Meg, Beth, and Amy" appear
# in the littlewomen.txt file
# Usage: bash countwords.sh
```
{: .bash}

These comments won't affect how the script runs, but help clarify what the script does,
and how to run it.
Try running the script again to prove this to yourself.

### Adding flexibility to a shell script
The benefit of this script is that it documents the analysis we've done, and prevents us from
making typing mistakes in the commands.
However, the (potential) downside is that it only ever runs those exact commands. 
We can make a shell script more flexible by using variables and passing command line arguments
when we run the script.

We're now going to edit our shell script to allow us to pass command line arguments to it.
The automatic variable `$@` refers to 'all the command line arguments'.

```
$ nano countwords.sh
```
{: .bash}

```
for word in $@
	do echo $word
	grep $word littlewomen.txt | wc -c
done
```
{: .output}

We can now run our script like this:

```
$ bash ./countwords.sh Jo Meg Beth Amy
```
{: .bash}

Clearly there is a tradeoff between flexibility and user-error. A script with no arguments
isn't subject to run-time typos, but it can only ever do that one same thing.

> ## Run a shell script, given arguments on the command line
> We can now search the file for any words given as arguments when calling the shell script.
> Try calling the script using different search terms!
> 
> > ## Solution
> > ```
> > $ bash ./countwords.sh any words you want to search for
> > ```
> > {: .bash}
> {: .solution}
{: .challenge}

> ## Update the comments in the shell script
> Our usage comment refers to the old version of the shell script.
> Update the comment to indicate how to use the current version of the script.
>
> > ## Solution
> > ```
> > # Usage: bash ./countwords.sh one_or_more_search_terms
> > ```
> > {: .bash}
> > It's a good idea to give variables meaningful names.
> > We can use this concept to help with documenting how to use our script.
> {: .solution}
{: .challenge}
