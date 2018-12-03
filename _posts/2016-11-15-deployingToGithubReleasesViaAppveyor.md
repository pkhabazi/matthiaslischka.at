---
title: Deploying to GitHub Releases via AppVeyor
layout: post
tags: [CI, Testing, AppVeyor]
---

Today I went through the pain of automating my [release](https://help.github.com/categories/releases/) deployment via [AppVeyor](https://www.appveyor.com/). Of course it turned out to be easy and I'm stupid since it was painful for me. However, now that everything is up and running I wanted to share my results.

What I wanted:

* Automated deployment to GitHub Releases
* Deploy one zip file
* Everything is configured via the appveyor.yml file within the repository
* Deploy only the master but every push to the master
* No Deployment/Build triggered by Tags

I'm deploying a .Net project with some Nuget packages.

And that's what I came up with for my _appveyor.yml_ file:

```yaml
configuration: Release

skip_tags: true

before_build:
    nuget restore
 
after_build:
    7z a nameOfZipFile.zip %APPVEYOR_BUILD_FOLDER%\projectName\bin\Release\projectName.exe %APPVEYOR_BUILD_FOLDER%\projectName\bin\Release\some.dll
 
artifacts: 
    path: nameOfZipFile.zip
    name: someUniqueNameForTheArtifact
    
deploy:
    release: projectName-v$(appveyor_build_version)
    description: 'some release description'
    provider: GitHub
    auth_token:
        secure: <YOUR ENCRYPTED GITHUB TOKEN>
    artifact: someUniqueNameForTheArtifact
    draft: false
    prerelease: false
    on:
        branch: master
```

Make sure you encrypt your GitHub token on the AppVeyor homepage!

See also the [AppVeyor documentation](https://www.appveyor.com/docs/deployment/) for additional information.