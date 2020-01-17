---
title: "Back to the Future Software"
date: 2019-02-06T23:53:00+01:00
draft: true
---

I read a post recently where developer complained about writing future-proof software. In this post I'll try to formulate my thoughts on this topic.

The first time I tried to write this I spent several hours and faied. It was more serious version than current one with toughts about complexity, Dijkstra, human limitations, etc. So here's back to the future software, take it easy version.

Q: What does it mean to write future-proof software?
A: In addition to implementing required functionality you also think about possible future extensions and then take this into account somehow.

My favourite use case when I don't need to think much about the future since as practise shows the future is pretty wild even locally. You just implement what you need and instead of wondering about new use cases you spend the time on checking code correctness (should solve a problem at hand) and if other people will understand it. After you did this other people will read you code and since it was written to be unerstandable by other people it would be easy for other people to modify it with new requirements.

Another use case when you know that something could happen to code and have few possible scenarios. In this case I like to do something similar i did before. To remember, I write code that does not include functionality related to possible future extensions. What's the difference with my favourite use case? In this case when I type new code I actually think about extensions and make sure that current implementation won't make those extensions hard or impossible to implement. But again, the final result solves only current problems and visually it does not contain the code from the future.

What is the problem with a code from the future. The obvious answer to you don't know the future.