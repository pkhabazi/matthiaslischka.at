---
title: TCR - Test && Commit || Revert
layout: post
tags: [Code, .Net, Testing, CI]
---

Yesterday I learned a new game in the [Coding Dojo Vienna](https://www.softwerkskammer.org/activities/CodingDojoVie20190415). It's an [idea from Kent Beck*](https://medium.com/@kentbeck_7670/test-commit-revert-870bbd756864) and an awesome tool to train correct refactoring.

You will need a codebase with existing tests for refactoring and some scripts to set up your tooling.

Done right your tooling will:
 - **tests your code** and
 - **commit the changes when all tests pass**
 - but **reset all changes when the tests fail**

## Setup

I had this setup done for Windows and .Net Core. All you need are some small batch scripts and your code in a git repository.

This should be very easy for every programming language and OS. Check out [ladders.js](https://github.com/dtanzer/ladders.js) by [dtanzer](https://github.com/dtanzer) and [codecop](https://github.com/codecop) for some JS example.

_test.bat_
```bash
dotnet test
```

_commit.bat_
```bash
git stage -A
git commit -m "working"
```

_revert.bat_
```bash
git reset --hard
```

We used [Emily Bache's](https://twitter.com/emilybache) famous [Tennis Refactoring Kata](https://github.com/emilybache/Tennis-Refactoring-Kata):
 
 - Check it out
 - Put the batch files in the `csharp` folder
 - commit the batch files! or they will get reverted as well when you fail at your first try
 - start refactoring
 - whenever you feel it run the following command in your CMD
 
 > ```bash 
 >  > test.bat && commit.bat || revert.bat
 > ```
 
This will run `dotnet test` and commit your changes when all tests pass or hard reset your changes when you made a mistake.

## Limits

While this is an awesome tool for a Coding Dojo and for training it has some clear limitations.
 
 - You can not do TDD in the classical approach. You can't write red tests first - they will get reverted.
 - You want your tests to be fast. Since your work is at risk every time you hit `TCR` you want to do it often and this could become frustrating when it takes too long.

## Why?

It trains you to work more cleanly. You are more likely to use failsafe refactoring tools like [R#](https://www.jetbrains.com/resharper/) when your work is at risk.

Trust me, it will be a funny experience. There will be moment when you hit `> test.bat && commit.bat || revert.bat` and are not sure if it will pass or reset all your code.

It for sure did reset our code at times. That were the most interesting moments. Some tests fail, you don't know why exactly and all your code is gone. So you can't just dirty fix the test but rather you have to rethink your approach. How could you fail at refactoring? **Done right refactoring should never change the behavior of the code at hand.**



---

*) [Kent Beck](https://medium.com/@kentbeck_7670/test-commit-revert-870bbd756864) is the author of "Extreme Programming Explained", "Test Driven Development", "Implementation Patterns" and more. All must-read books.