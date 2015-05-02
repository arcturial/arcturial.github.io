---
layout: post
title: Behat Syntax Highlighter For Sublime Text
fullview: true
---

I recently starting writing tests using [Behat](http://docs.behat.org/en/v2.5/) and had the problem that [Sublime Text 2](http://www.sublimetext.com/) did not have default functionality to help highlight my `.feature` tests (having everything display white on black can get tedious fast).

So…I hit Google and found one or two other plugins that claim to do the job, but after trying them…I wasn’t a big fan. Thus, I created my own plugin.

### Instructions

Download or clone the repository to your `Sublime Text2/Packages/User/` or `Sublime Text2/Packages` folder. You can also choose to only copy `Behat/Syntax.tmLanguage`

### How To Use

After copying the package to your user packages folder, you should be able to activate it in Sublime by going to `View > Syntax > Behat`.

**Update:** My repository has been merged with [https://github.com/omissis/sublime-behat-syntax](https://github.com/omissis/sublime-behat-syntax) which should provide a complete syntax highlighter for Behat features.