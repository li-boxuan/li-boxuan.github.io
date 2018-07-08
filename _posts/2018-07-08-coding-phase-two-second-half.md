---
layout: post
title: GSoC' 2018 Coding Phase 2 Week 3 & 4 report
categories: gsoc
description: This is biweekly report of week 3 & 4 in coding phase 2.
keywords: gsoc, report
---

This post is longer than the last one, as something beyond my plan
happened. Read the post to see what happened ;)

### Continue unfinished work during coding phase 1

My last blog post (first report of coding phase 2) already included
this, and I said I was hoping it could be merged soon.
The fact is,
[Implement meta-review scoring & ranking system](https://github.com/coala/community/pull/143)
is still pending review. It receives reviews and suggestions from time
to time, so I have been continuously improving its quality. This is
quite common in coala community, considering its size is large. coala
community, especially our maintainers do extremely careful reviews
from time to time. I can feel that I learn a lot more from refining
my code according to reviews, than just pure coding. The good news
is our maintainer John says the pull request is looking quite good now,
so it should hopefully be merged in short.

### Setup GraphQL client

This is the most significant work I've done during the past two
weeks. It has two parts: setup a base client and use that to rebuild
sync functionality. See pull request [gh-board#67](https://github.com/coala/gh-board/pull/67).

#### Setup base client

This is about writing a 'library' that (currently) only used by our own website.
The gh-board uses [octokat.js](https://github.com/philschatz/octokat.js) before.
That library makes use of GitHub API V3 (REST API), which is a bit out of date.
Thus, I make use of GitHub API V4 (GraphQL) to build a new library. The interesing
part is that when I build this library, I deliberately designed chainable methods.
This is what I've never built before. The reason behind that is I want to match
the existing octokat.js style. Also, I find it much cleaner and easier to use.

An example is as follows:

```javascript
fetchAllIssues = await Client.getGraphQLClient()
  .repo(repoOwner, repoName)
  .issues({sort: 'UPDATED_AT'}).
  .fetchAll({per_page: 100});

fetchAllReactions = await Client.getGraphQLClient()
  .repo(repoOwner, repoName)
  .reactions({pr_number, per_review: 20})
  .fetchOne({per_page: 100});
```

#### Use newly built client to sync issue

An important benefit of the newly built client is that it supports reactions fetching.
It is a bit cumbersome to fetch reactions using GitHub API V3. I changed `issue-store.js`
and use the newly built client to synchronize issues and pull requests. This is easy
with the newly built client!

### Solve coala & coala-bears issues

During the past week, something out of my plan happened.

I added a new change to coala coalib in early April:
[TextPosition.py: Raise exception for zero offset](https://github.com/coala/coala/pull/5367)
That pull request adds a functionality that coala will raise an exception
if any bear reports a message to user which contains 0 line or column number.
That is because according to coala's convention, line and column number
start at 1.

Recently John decided to use up-to-date coalib on coala-bears repo. Previously,
coala-bears uses only pre-released version of coalib, not git version. That means
my changes would take in effect on coala-bears repo. Then things happened: my new
functionality detected that many bears violated coala convention - they reported
either 0 line number or 0 column number, or both. Although they are totally unrelated
to my own project, our maintainer John asked me to solve them, as nobody knows them
more than me.

The lucky thing is I've got my sword:
[linter: Add new parameters to normalize numbers](https://github.com/coala/coala/pull/5350).
That means to mapping third party's convention to coala's convention would be extremely easy
now. However, this work is more of an investigation task. I need to investigate which convention
those third party linters use - this is not trivial at all. For example, if you take a look
at issue [CPPLintBear violated one-based offset convention](https://github.com/coala/coala-bears/issues/2562),
it is not easy to fix as cpplint does not have documentation clarifying which convention it
is using. In fact, most linters lack documentation regarding offset convention. After
digging into their source code, I found that they use 0-based line number as follows:


They use 0-based line number.
See https://github.com/cpplint/cpplint/blob/master/cpplint.py#L1383

```python
def Error(filename, linenum, category, confidence, message):
```

and https://github.com/cpplint/cpplint/blob/master/cpplint.py#L2133-L2135

```python
for linenum, line in enumerate(lines):
  if unicode_escape_decode('\ufffd') in line:
    error(filename, linenum, 'readability/utf8', 5,
          'Line contains invalid UTF-8 (or Unicode replacement character).')
```

After solving some other linter issues, I found that checkstyle is a bit special.
They use 1-based line number, but they would report 0 line number if the error
message is related to the whole file. My current sword does not work in this case.
Thus, I made a new pull request and added a new linter option to coalib:
[Linter: Add option to remove 0 number](https://github.com/coala/coala/pull/5601).

Now all the commits are either merged or under review. See also
[Fix several bears which violate 1-based line/column convention](https://github.com/coala/coala-bears/pull/2413).

### Conclusion

The end of coding phase 2 is close. I am still refining pending pull requests,
and I'll also send some commits to upstream repo (Ryanprior's) in short.
