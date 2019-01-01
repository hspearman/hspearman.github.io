---
layout: post
title: "Ditch Your Fancy Note-taking Apps; Apply Unix Philosophy Instead"
date: 2018-12-30 14:00:00 -0600
---

I'm big on taking notes. It's a helpful way to keep track of ideas, tasks, goals, reference material, and more.

Over the years, I've tried many note-taking apps like OneNote, Evernote, etc. But with each new tool, it felt like I was trading pros and cons but never was 100% satisfied with any of them.

Recently, I ditched my fancy note-taking apps and went back to basics: **Markdown, Visual Studio Code, and Dropbox.**

And it's been working out great.

## Back to Basics

Many note-taking apps try to do everything, but not necessarily well.

Evernote was slick, but its' text editor kept jumbling the format of my files. OneNote had a great text editor, but no support for code blocks among other pains. At the end of the day, each tool fell short in its' own special way. 

Unsatisfied with these apps, I decided to apply the Unix philosophy instead: pick tools that **do one thing and do it well**, then combine them into one fluid workflow.

### Markdown

First off, write your notes using Markdown. Markdown is a lightweight syntax for formatting text, and is a pretty popular standard across the web (for example, it's the markup language used for GitHub READMEs). 

**It conveniently lets you format text using, well, _text_**. For example, bold something simply by wrapping it with asterisks:

```
**Oh so bold.**
```

No more strange shortcuts or obscured buttons buried in an app's toolbar. Format notes without ever reaching for your mouse. Just type!

### Visual Studio Code

Great: we have a way to format notes. Now what's the best way to edit and view them?

Unless you've been living under a rock, you have likely heard people sing their praises for Visual Studio Code. It's my daily driver for coding these days-- and with **built-in markdown support and a plugin for vim keybindings**, it's makes for a great text editor too.

To take advantage of VSCode's Markdown support, first create a file with the `.md` extension. To preview your text as you type, bring up the Command Palette and select `Markdown: Open preview to the side`.

![Preview Markdown VSCode](/public/imgs/markdown_preview.gif)

Voila.

### Dropbox

It's important to back-up your notes and share them when needed. For that, use Dropbox.

Create notes in a Dropbox folder to easily back-up and sync them across your devices. When the time comes to share your notes with someone, you can easily create a shareable link and send it their way.

## Closing Thoughts

Dedicated note-taking apps often provide an imperfect experience. Instead, pick tools that do one thing really well and use them together.

Markdown is a universal markup language that offers a simple yet effective way to format your documents. Visual Studio Code's built-in Markdown support and vim keybindings make it a great text editor. Finally, Dropbox backs up your data and makes it easy to share with other people.

If you're not much of a note-taker, I recommend picking up the habit. If you're already in the habit, try switching up your workflow with these three tools instead. 

## Addendum

### Vim

Consider learning vim if you haven't already: its keybindings make typing quicker and genuinely more fun. Enter `vimtutor` into any unix shell and run through the tutorial a couple times. You'll pick it up faster than you think.

If you do decide to take the dive, Visual Studio Code has a pretty excellent vim extension to support your efforts.

### On the Go

The only downside I've found with this workflow is it's not so great on-the-go: you can easily read your notes via the Dropbox app, but writing them is another story. 

That's why, for everything else, I go analog: writing down notes in a physical journal.

Grocery lists, daily to-dos, etc. Any non-computer-based task that lends itself better to the physical medium. As an added bonus, I recommend the [bullet journal](https://bulletjournal.com/) framework as a good system that keeps your journal organized.

