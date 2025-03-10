---
title: Command Line Tools
subtitle: Useful tips and tricks
tags:
  - computer
categories:
  - Blog
last_modified_at: 2022-06-30
---

When using properly, command line is often the easiest and fastest way to get tasks done, even though the same thing can be also accomplished in other ways like using Python, Perl, etc..
Many useful little tricks can be found on the internet. I have collected some of them here.

---

## `scp` with regular expression

It is not guaranteed that the terminal you use can recognize regular expression on the remote machine. The magic here is

```sh
scp "user@machine:/path/[regex here]" .
```

## Finding and replacing text within files

```sh
sed -i 's/original/new/g' file.txt
```

Explanation:

* `sed` = Stream Editor
* `-i` = in-place (i.e. save back to the original file)
* The command string:
  * `s` = the substitute command
  * `original` = a regular expression describing the word to replace (or just the word itself)
  * `new` = the text to replace it with
  * `g` = global (i.e. replace all and not just the first occurrence)
* `file.txt` = the file name

## Monitoring log file in real time

```sh
tail -f log
```

This works, but the downside is that `tail` reads the whole file into buffer. As an alternative, using `less` is a more elegant approach:

```sh
less +F log
```

## Returning the last n modified file in directory in time order:

```sh
ls -Art | tail -n 1
```

or in reverse order:

```sh
ls -t | head -n 1
```

## Piping selected files into tar

```sh
ls -Art | tail -n 5 | tar czvf out.tar.gz -T -
```

## Avoiding file auto purge

On many file systems, there may be some rules for file housekeeping. One trick to avoid it is to touch each and every file in the repository. This can be done through the following command:

```sh
find /home/example -exec touch {} \;
```

## Counting Files

```sh
ls -F | grep -v / | wc -l
```

`ls -F` list all files and append indicator (one of */=>@|) to entries'
`grep -v /` keep all de strings that do not contain a slash;
`wc -l` count lines.

## Checking missing sequence files

Assume the files share the pattern `FILE_DDD.txt`.

*Example 1*:

```sh
for i in {1..14}
do
seq=`printf "%03d" $i`
if [ ! -f "FILE_${seq}.txt" ]
then
echo "FILE_${i}.txt"
fi
done
```

Assume a series of files with numbers indicating the day of a year and the hour of day:
> GLDAS_NOAH025SUBP_3H.A2003001.0000.001.2015210044609.pss.grb
> GLDAS_NOAH025SUBP_3H.A2003001.0600.001.2015210044609.pss.grb
> GLDAS_NOAH025SUBP_3H.A2003001.1200.001.2015210044609.pss.grb
> GLDAS_NOAH025SUBP_3H.A2003001.1800.001.2015210044609.pss.grb

```sh
for a in file_{001..365}.{00..18..6}.txt
do
  [[ -f $a ]] || echo "$a"
done
```

Note the usages of `..` in the above examples. This is very useful to generate a continous list of strings

Assume just number ordering:

```sh
ub=1000 # Replace this with the largest existing file's number.
seq "$ub" | while read -r i; do
    [[ -f "$i.txt" ]] || echo "$i.txt is missing"
done
```

Someone suggested `awk`, but I am not familiar with it at all.

## List file names using regular expression

*Example 1*: finding files within sequence and deleting

```sh
ls | grep -P "^08[5-9].*[0-9]" | xargs -d "\n" rm
```

*Example 2*

```sh
find your-directory/ -name 'A*[0-9][0-9]' -delete
```

## Removing prefix from files

<script src="https://gist.github.com/larshaendler/3c477182717d32a4fc64070c283d24ad.js"></script>

## Changing file extensions

```sh
for f in *.html; do
    mv -- "$f" "${f%.html}.php"
done
```

## sudo

`sudo -s`: run shell by root without changing a directory, also env vars (that could be set in, e.g. `.bash_profile`) wouldn’t be imported.

`sudo -i`: run shell by root, import env vars and change directory.

`su -`: first change user, then run shell. It means that all env vars were cleaned and "clean" root session was executed.

`su`: command doesn’t clean env vars, it just changes a user to root and keeps env vars for old user.

---

Many more to be added later! Some additional private notes on scripting are listed in [this repo](https://github.com/henry2004y/ScriptingNotes).
