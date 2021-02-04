---
title: Rounding -0.5 is easy, right?
layout: post
tags: [Code, Funny]
---
 > What is -0.5 "_default_" rounded?

**Think about this before you continue reading!**

Well, we had some fun today at work with this question and even more fun when we put our preferred programming languages to the test. Behold the results.

| Input      | 0.6 | 0.5 | 0.4 | -0.4 | -0.5 | -0.6 |                    |
| ---------- | --- | --- | --- | ---- | ---- | ---- | ------------------ |
| C#         | 1   | 0   | 0   | 0    | 0    | -1   | `Math.Round(-0.5)` |
| Java       | 1   | 1   | 0   | 0    | 0    | -1   | `Math.round(-0.5)` |
| Python     | 1   | 0   | 0   | 0    | 0    | -1   | `round(-0.5)`      |
| C          | 1   | 1   | 0   | -0   | -1   | -1   | `round(-0.5)`      |
| Go         | 1   | 1   | 0   | -0   | -1   | -1   | `math.Round(-0.5)` |
| PHP        | 1   | 1   | 0   | -0   | -1   | -1   | `round(-0.5)`      |
| JavaScript | 1   | 1   | 0   | -0   | -0   | -1   | `Math.round(-0.5)` |
| Excel      | 1   | 1   | 0   | 0    | -1   | -1   | `ROUND(-0.5;0)`    |

I like `-0`. Did honestly not know [that](https://en.wikipedia.org/wiki/Signed_zero).

![surprise](/assets/posts/2019-08-30-Rounding-minus-0.5_memeface.png){:height="400px" }
