---
title: Jekyll and docker-compose
layout: post
---
Everyday I use docker not only as a tool to ship software in isolated containers but also as a tool for local development. It allows me to play aground with stuff but keep my machine clean at the same time. A little over a year ago I started [using docker for my local Microsoft SQL Server](http://matthiaslischka.at/2017/10/14/hello-docker-for-windows-world/). Ever since then I find more and more use for it.
The latest utilization of docker in that fashion and a good representation that I want to share is the maintenance of my Jekyll-homepage.

![Jekyll + docker-compose]({{ "/assets/posts/2018-12-22-jekyll-docker-compose/jekyll-and-docker-compose.png"}})

## Why use docker for Jekyll

Jekyll is a [Ruby](https://www.ruby-lang.org) tool and as such you would have to [install a full Ruby development environment](https://jekyllrb.com/docs/installation/). Since I do not need Ruby for anything else at the moment I want to keep my system clean of it. See [here](http://matthiaslischka.at/2017/10/14/hello-docker-for-windows-world/) on how to setup docker-for-windows.

## docker-compose

I use docker-compose to handle the container. The following `docker-compose.yml` file configures everything needed.
```yml
version: '3'

services:
  jekyll:
    image: jekyll/jekyll:latest
    command: jekyll serve --watch --force_polling --verbose
    ports:
      - 4000:4000
    volumes:
      - .:/srv/jekyll
```

 - defines one service called `jekyll`
 - uses the [Jekyll docker image](https://hub.docker.com/r/jekyll/jekyll)
 - command to start Jekyll
 - map from local port 4000 to contaner port 4000
 - map from current local folder (`.`) to container working folder (`/srv/jekyll` - it's a Linux container)

  > Make sure to have docker configured to `Linux containers`

## Create a new homepage

Copy the `docker-compose.yml` file in an empty folder and run `docker-compose run jekyll /bin/bash` from the command line. This will download the [Jekyll docker image](https://hub.docker.com/r/jekyll/jekyll), start the container and tunnel the Linux bash of the container to the command line. You can now enter Linux commands that are executed within the docker container.
Run `jekyll new . --force` in the bash to setup a new homepage. (`--force` is needed because Jekyll prefers empty folders, but we do already have the `docker-compose.yml` file in the folder.)

Notice that the docker-container folder is mapped to our local folder via the `volumes` option in the `docker-compose.yml`.

Once `jekyll new . --force` is finished all necessary Jekyll files for a new blog have been added to the folder. Not only in the Linux container but also in our host Windows folder. 

Now the homepage can be served.
	
## Serve the homepage

Just run `docker-compose up` from the command line. That will start the docker-container and run Jekyll. Folders and ports are mapped and the homepage can be browsed under `http://localhost:4000/`.

## Et voilà

![GIF animation of the whole setup]({{ "/assets/posts/2018-12-22-jekyll-docker-compose/animation.gif"}})