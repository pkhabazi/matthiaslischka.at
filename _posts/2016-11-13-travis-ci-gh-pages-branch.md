---
title: Travis CI and gh-pages branch
layout: post
tags: [CI, Testing, Travis CI]
---

To my surprise [Travis CI](https://travis-ci.org/) does ignore the _gh-pages_ branch by default. The branch will not be listed in Travis CI and builds will not be triggered. My problem was that I had a repository with only one _gh-pages_ branch. It contained some static html content that i wanted to [html-proof](https://github.com/gjtorikian/html-proofer).

Easy solution was to create a _master_ branch instead, change the github pages branch and the github default branch to that _master_ and delete the old _gh-pages_ branch.

Another possible solution would be to explicitly enable the gh-pages branch in the _.travis.yml_ file

``` yaml
branches:
   gh-pages: true
```

But i saw no extra beauty in that. Imho it is weird that the gh-pages branch is blacklisted by default.