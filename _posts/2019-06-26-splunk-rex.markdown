---
layout: post
title:  "Using rex command to extract fields in Splunk"
date:   2019-06-26 21:02:00 +0200
tags: [splunk, regex]
---

# TODO
4. Change name of file to match title
5. Check language
6. Change date for current

---

In one of the projects that I'm currently working we are using Splunk as a common logs/metrics viewer. It is a very powerful tool but it took me some time before I started to feel comfortable with it. One of the most often used command is `rex`.

Among couple of things you can do with `rex` command - today I would like quickly talk about most basic feature - extracting fields with regexp. To be more strict - with unanchored regex named capture groups. I've never been knowledgable about regexp - and because of that it was hard for me to understand how `rex` works. Because of that - let me start with quickly introduction of those regexp related concepts.

## Unanchored regexp

This one is simple - unanchored regexp is the one that does not contains (surprise, surprise...) anchors. Two most used anchors are `^` and `$` - that matches beginning and end of the line respectively. So those one cannot be used together with `rex` command.

## Named capture groups


When you are writing regex you can use so called capture group to "extract" "subpart" of matched text. For example if I would like to find txt files I can do something like this: `\w*.txt` - but if I am interested onlt with name without extension - I can do this: `(\w*).txt`. The parenthesis that I added created a group which holds only file name. You can have more than one group - and each of them will have it's own index.

We can give a name to our group, so we can access such group later by this name. Look at this: `(?<filename>\w*).txt` - I added `?<filename>` to the previous regexp and that part resulted with naming my capturing group with a name `filename`. In splunk we can used named captured group to create variables.

## Rex syntax

Probably the most basic syntax for `rex` command will looks like this:


```
rex field=[name] [regex]
```

The `regex` part is a regex with named captured group inside that we want to use to extract field. Name of the group will be name of new field. The `name` is a name of the field that we would like use our regex on.

Let's say that we have a logs with field `files` that contains comma separated names of the files like this:

```
file1.txt, file2.txt, file3.txt
```

In order to extract just file names we can use rex command in the following way

```
rex field=files (?<filename>\w*).txt
```
_(Small hint: if your regexp contains spaces put it inside quotes - otherwise you will have syntax error)_

It is possible to extract multiple fields at once in one run of `rex` command

## Summary

At the first glance you might think that this command might be useful but not very much. The thing is if you extracted something into field - you can use this field later for other commands - espacially as part of `by` statement for commands like `stats`, `chart` or `timechart`. Because of that `rex` quickly became for me one of the most often used Splunk command.


## Links
- [Documentation of rex command](https://docs.splunk.com/Documentation/Splunk/7.3.0/SearchReference/Rex)
- [More info about capturing groups](https://javascript.info/regexp-groups)
- [Regex debuger](https://regex101.com/) (helps test your regex)
