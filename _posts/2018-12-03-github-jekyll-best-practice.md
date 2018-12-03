---
title: GitHub Jekyll Theme best practice
layout: post
tags: [GitHub, Jekyll]
---
My blog is based on [Jekyll](https://jekyllrb.com/), markdown files transformed to static html, hosted on [GitHub Pages](https://pages.github.com/). I just now changed the theme I use. Thereupon I optimized the way I arrange my repositories around that.

Up until now I used to fork a [Jekyll](https://jekyllrb.com/) theme. With that I could add my personal content and still pull-request changes back to the original. When I wanted to change the theme now I realized that I can not change the fork. It will always be a fork from the first theme I used. GitHub itself states in its helpfiles that you have to contact the support team if you want to change a fork to a self-standig repo. I did not want to harass them with my little problem.

## My solution
 - Create a new empty repo on GitHub that will be your blog
 - Clone the GitHub repo you want your blog to be based on locally
 - Rename "origin" to "upstream" `$ git remote rename origin upstream`
 - Add your own empty repo as origin `$ git remote add origin http://github.com/YOU/YOUR_REPO.git`
 - Push master/gh-pages branch to your empty GitHub repo `$ git push origin master`
 - Fork the GitHub repo your blog is based on
 - Add the fork as _personalUpstream_ `$ git remote add personalUpstream https://github.com/YOU/FORKES_REPO.git`

You will end up with a personal repository that has all the commits from the base repository and two upstreams, the base repository and your fork. In this repository only content data should be added. (_config.yml, _posts/, images/, assets/ ...)

_upstream_ is the link to this base repository. This allows you to `fetch` all changes to the base and merge them in your repo. Use this to get bugfixes, new features, ...

The fork of the base repository is for all _your changes_ that are not content related but rather changes to the base. Commit those in this repository and use the _personalUpstream_ to `fetch` and merge them into your "content repository". This allows you to still create pull reqeusts back to the base without having your content hard-linked (fork) to it.

If you want to change your theme later you just need to change _upstream_ and _personalUpstream_ and overwrite the base-data with your new base. You keep the commit history of all your content data.

When everything is setup you can test your "content-repo" with `$ git remote -v`. It should look like:
```cmd
origin  https://github.com/YOU/YOUR_REPO.git (fetch)
origin  https://github.com/YOU/YOUR_REPO.git (push)
upstream        https://github.com/SOMEBODY/BASE_REPO.git (fetch)
upstream        https://github.com/SOMEBODY/BASE_REPO.git (push)
personalUpstream        https://github.com/YOU/BASE_REPO.git (fetch)
personalUpstream        https://github.com/YOU/BASE_REPO.git (push)
```

Open for further ideas or other solutions...