+++
title = "How I do Journaling"
date = "2020-06-01"
description = "An article regarding the way I do day to day journaling with jrnl.sh"
categories = [
    "Stuff",
]
tags = [
    "Data",
]
+++

This article is about [jrnl.sh](https://jrnl.sh/) and how I use it in my day to day life.
<!--more-->

### Why I journal

There are a few reasons I journal, let me jot them down real quick and give a brief overview of each:
- time tracking
- thought organization
- future reference to past problems
- setting and getting to the goalpost
- seeing progress made
- venting

#### Time Tracking

Currently, I'm working as a consultant. As such, time tracking is an *essential* part of my day to day activities, and while I don't like getting *too* granular, it does look good to be able to show what you've been up to for every few hours of the day. Journaling makes this quite easy as I often make notes regarding what I'm busy with, thus I can usually just use the heading and timestamp from a journal to keep track of what I've done during the day. This also helps a lot during standups.

#### Thought Organization

About to go out for lunch? Make some notes of what you're busy with. Knocking off? Write down what you're leaving for yourself to do tomorrow. Going for a walk? Don't lose the flow, freeze your thoughts in text. The sheer usefulness of being able to sit down and immediately remember what I was doing and thinking before I left is immense.

#### Future reference to past problems

Ever encounter an obscure problem, solve it, and then a few months down the line you have the same problem again? Just *wait* for the great feeling you get when the second time the problem shows up you've already got a solution at hand

#### Setting and getting to the goalpost

I sometimes lose track of what I'm trying to achieve, and it helps to bring back my focus towards what I want to accomplish when I write down what I've been doing. Likewise, setting a goal makes sure I don't stray too far from where I want to go and prevents [yak shaving](https://en.wiktionary.org/wiki/yak_shaving).

#### Seeing progress made

Sometimes after a long week of problem after problem, roadblocks and red tape, sweat and tears, staring at a long logbook of every challenge you've overcome since you started with something truly revitalizes the soul, and gives you a second wind.

#### Venting

Let's be honest, *everybody* likes to complain. Especially when someone else is agreeing with your complaints, and there's no one that agrees with me more than myself. I find that a quick two sentence complaint helps me rid myself of the weight of something bothering me. Weather is cold? Complain, dress warmer, and move on. Badly designed software getting to you? Complain, take a deep breath, and realize things like Citrix are a part of life.

### How I Journal

Personally I like to use [jrnl.sh](https://jrnl.sh/). Let me walk you through how I use it.

#### For work
When I want to write a journal entry regarding something work related, I do it using a "work" journal. I run `jrnl work` and up pops a vscode window with the following pre-made template:
```txt


BODY:

NOTES:
```

This allows me to quickly pin an idea or talk about an issue. Here's an example of how I would fill it up:
```txt
Azure disks don't mount when instance is created

BODY:
@Azure is weird, and for some reason someone decided it would be a good design to not mount any virtual disks before an instance starts up, they are instead mounted after startup, resulting in <current project>'s bootstrap section to fail. I will have to implement a check to see when the device is mounted before the script can continue.

NOTES:
It might be a good idea to just attach the first LUN device since the disk will always be LUN0
```
looking at the latest entry:
```txt
❯ jrnl work -n 1
2020-06-02 13:46 Azure disks don't mount when instance is created
| BODY:
| @Azure is weird, and for some reason someone decided it would be a good
| design to not mount any virtual disks before an instance starts up, they are
| instead mounted after startup, resulting in <current project>'s bootstrap
| section to fail. I will have to implement a check to see when the device is
| mounted before the script can continue.
| 
| NOTES:
| It might be a good idea to just attach the first LUN device since the disk
| will always be LUN0
```
Similarly I can look for tags on things that were previously relevant:
```txt
❯ jrnl life @shrimp
2020-06-02 13:51 Finally got the fishtank set up
```

Of course, just these text snippets don't deal it justice. It actually looks quite nice:
![jrnl.png](/jrnl.png)

#### For life

Life journals are for every day random thoughts or ideas that I just want to not down, I don't use the template unless I find it necessary, and as such I can just use **jrnl** from the command line:
```bash
❯ jrnl life Starting on msngr. Ive started working on @msngr, it should end up being a project that syncs jrnl entries to @s3, possibly other cloud storage providers.
[Entry added to life journal]

❯ jrnl life -n 1
❯ jrnl life -n 1
2020-06-02 20:25 Starting on msngr.
| Ive started working on @msngr, it should end up being a project that syncs
| jrnl entries to @s3, possibly other cloud storage providers.

```

Neat, simple, and quick. Perfect for jotting down an idea that might escape you. For those curious, you can look at the progress of [msngr here](https://github.com/Neels-v-Wyk/msngr)

#### jrnl config

Here is my `jrnl.yaml` configuration file:
```yaml
colors:
  body: none
  date: cyan
  tags: yellow
  title: blue
default_hour: 9
default_minute: 0
editor: '/usr/bin/code --wait'
encrypt: false
highlight: true
indent_character: '|'
journals:
  default: /home/neels/.local/share/jrnl/work.txt
  work: /home/neels/.local/share/jrnl/work.txt
  play: /home/neels/.local/share/jrnl/life.txt
linewrap: 79
tagsymbols: '@'
template: '/home/neels/.local/share/jrnl/template.txt'
timeformat: '%Y-%m-%d %H:%M'
version: v2.4.2
```

Have fun journaling!
