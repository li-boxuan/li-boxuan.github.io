---
layout: post
title: GSoC' 2018 Coding Phase 1 Week 1 & 2 report
categories: gsoc
description: This is biweekly report of week 1 & 2 in coding phase 1.
keywords: gsoc, report
---

The most exciting part of Google Summer of Code: Coding Phase started two
weeks ago. This is my first biweekly report of coding phase one.

My coding phase one is about fetching review comments with reactions,
processing them and generating
[meaningful analysis](https://github.com/coala/cEPs/blob/master/cEP-0019.md#analysis-of-meta-reviews).
I have built a custom issue fetcher on
[coala/gh-board](https://github.com/coala/gh-board/) repo during the bonding
period. During the first two weeks I added other information we need, including
reviews and reactions of pull requests.

At the very beginning I thought it was going to be a piece of cake, but later
I came across a challenge: GitHub API rate limit became bottleneck. We need far
more than
[5,000 points](https://developer.github.com/v4/guides/resource-limitations/)
to fetch all info we need. Moreover, API hits have nothing to do with actual
data returned. GitHub calculates rate limit score based on the query itself.

As I pointed out in
[this comment](https://github.com/coala/gh-board/pull/39#issuecomment-390413685),
Consider the following pseudo query:

```python
for pr in first_100_pull_requests:
    for review in first_100_reviews:
        for comment in first_100_comments:
            for reaction in first_x_reactions:  # x isn't important here
                yield content
```

The API has to connect to each of the 100 x 100 x 100 potential total comments to
get the list of reactions. That leads too 100 x 100 x 100 / 100 scores, i.e. 10,000
score, which already exceeds maximum hit rate. The worst thing here is no matter how
much information is returned, we always pay the same. That is, even if the pull
requests have no reviews at all, we still consume 10,000 score.

I've tested different settings and finally I adopted the setting that we only
fetch 10 comments and 9 reviews x 9 discussion comments from every single pull
request. With this setting, hit limit is enough for us to fetch all history
data since 2016 March, when reaction functionality was added to GitHub.

Later, I thought there should be some better way to do this. Instead of fetching
review comments and reactions together, I made them separate. We get review info
first and see if there is any reaction given to any comment within a pull
request. If there is, then we query on that pull request to retrieve reaction
info. After doing that, we could fetch 100 comments and 20 reviews x 100
discussion comments from every single pull request since 2016 March without
exceeding API rate limit.

However, a new problem appeared. As I pointed out in
[this comment](https://github.com/coala/gh-board/pull/39#issuecomment-391358332),
the Netlify deploy time limit (15 mins) became the new bottleneck. I made all
queries run sequentially at the beginning, leading to deploy time out. Then I
tried to make use of concurrency. However, I found out GitHub doesn't like
this. Not sure if it was API abuse, but it turned out some queries would fail
if they are running too fast. Obviously, there is a tradeoff between successful
rate and deploy time. Luckily, I found a balance in the end. I set concurrency
level to 50 and use timeout retry. Also, I change the earliest date from
2016-03-10 to 2017-01-01. Now it's able to finish all fetching in 12 mins.

That was only the very first part of the whole project. We need raw data for
all other actions. During the coding phase one, I need to build a Django App
in [coala/community](https://github.com/coala/community) repo. The idea behind
it could be seen at the
[cEP](https://github.com/coala/cEPs/blob/master/cEP-0019.md#analysis-of-meta-reviews).
During the first two weeks, I finished the design of it and created a pull
request [community#134](https://github.com/coala/community/pull/134).

I've also done some other tasks during the first two weeks. I merged a pull request
of small size which fixes a bug of gh-board:
[coala/gh-board#41](https://github.com/coala/gh-board/pull/41). I also created a
pull request to upstream repository:
[Modernise React code, update packages, fix warnings and improve build](https://github.com/philschatz/gh-board/pull/147).
Creating that large pull request was really a painful experience: our repository has
been far away from the original (the upstream of our upstream) repository. A
simple cherry-pick didn't work at all. I rebased all commits and dropped them
one by one. Finally a pull request was there. If the author is going to be
inactive for a long time, we may consider create a pull request to our
upstream repo.

I have been working on the processing part on coala/community repo. Hopefully
a large pull request will be created next week.
