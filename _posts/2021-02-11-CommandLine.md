---
title: Command Line Tools
subtitle: Useful tips and tricks
tags:
  - computer
categories:
  - Blog
---

When using properly, command line is often the easiest and fastest way to get tasks done, even though the same thing can be also accomplished in other ways like using Python, Perl, etc..
Many useful little tricks can be found on the internet. I will collect some of them here.

* `scp` with regular expression

It is not guaranteed that the terminal you use can recognize regular expression on the remote machine. The magic here is
```
scp "user@machine:/path/[regex here]" .
```

* Find and replace text within files

```
sed -i 's/original/new/g' file.txt
```
Explanation:

  * `sed` = Stream EDitor
  * `-i` = in-place (i.e. save back to the original file)
  * The command string:
    * `s` = the substitute command
    * `original` = a regular expression describing the word to replace (or just the word itself)
    * `new` = the text to replace it with
    * `g` = global (i.e. replace all and not just the first occurrence)
  * `file.txt` = the file name
```

* Monitor log file in real time

```
tail -f log
```

This works, but the downside is that `tail` reads the whole file into buffer. As an alternative, using `less` is a more elegant approach:
```
less +F log
```

* Return the last n modified file in directory in time order:
```
ls -Art | tail -n 1
```
or in reverse order:
```
ls -t | head -n 1
```

* Pipe selected files into tar:
```
ls -Art | tail -n 5 | tar czvf out.tar.gz -T -
```

Many more to be added later!
