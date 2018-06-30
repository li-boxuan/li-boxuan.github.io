---
layout: post
title: GSoC' 2018 Coding Phase 2 Week 1 & 2 report
categories: gsoc
description: This is biweekly report of week 1 & 2 in coding phase 2.
keywords: gsoc, report
---

This is going to be a short post as there's nothing special to share
during the past few weeks.

### Continue unfinished work during coding phase 1

I didn't ever imagine that my work in coding phase 1 would be put off
to coding phase 2. I submitted a
[pull request](https://github.com/coala/community/pull/143)
at the end of week 3, coding phase 1. I thought a week would be quite
enough to get proper reviews and get it merged. I was too optimistic
at that point. The fact is, it is still pending review at the moment:
I was getting reviews from time to time and refining it from time to
time. Now I am hoping it can be merged during coding phase 2 ;) Although
it is irrelevant to my work in coding phase 2 & 3, I still hope I could
close my coding phase 1 milestone in short.

### Fix broken CI on gh-board repo

As what happened in coding phase 1, I spent a lot of time struggling with
CI. This time Travis. Here is the issue:
[Travis failing due to webdriver-manager issue](https://github.com/coala/gh-board/issues/60).
Luckily, I fixed in the end. I have also sent it to the upstream repo
[philschatz/gh-board#147](https://github.com/philschatz/gh-board/pull/147) and
fixed the broken CI there.

### Fix `state=closed` filter bug on gh-board

I found a bug with gh-board and filed an issue for that:
[Applying `closed` filter returns `no issues`](https://github.com/coala/gh-board/issues/61).
It seems it was a careless mistake made by the original author. Luckily, I've fixed
it. It is critical when people want to do meta-reviews for closed
pull requests. 

### Setup meta-review in need

This is the most significant work I've did in the past two weeks.
See [Pull request](https://github.com/coala/gh-board/pull/65)
This is how it looks like:
![](https://user-images.githubusercontent.com/25746010/41981546-c098ee0c-7a5b-11e8-99fa-000cd80c366c.jpg)
It is under pending review at the moment.

### Conclusion

I am currently working on the issue sync part. A pull request will be
submitted during the weekend.
