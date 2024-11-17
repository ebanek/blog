---
title: 'Programming with LLMs and good feedback loops'
description: 'I am never using online chat LLMs for programming again'
pubDate: 2024-11-17
lastUpdateDate: 2024-11-17
---

This is my second time trying to make an extension with the help of LLMs. This time the project was less defined and larger (roughly 20 hours of work). The help I received from the LLM was most useful during the start - it could quickly make a working skeleton of the project. But the more specific my requirements got, the less useful the LLM was. One conclusion that I repeated from my previous experiment was that the LLM has to be integrated into the coding environment.

### Programming offline and using LLMs online

Similarly to my previous post, it's painfully obvious that coding online with LLMs is more painful compared to using it integrated into the environment.

For one, copy-pasting code back and forth to onlne interface LLMs seems to require a lot of tokens - and the only moment that a lot of text from LLMs is useful is at the very start. At the start, it generates a lot of correct-looking code from the requirements. This is a great time-saver since reading code is faster than writing and it lets you start focusing on the crucial parts of the app. But later on, every little change that should be made requires copy-pasting code - and copy-pasting makes for slower iteration loops than just staying in a single environment.

The second way that using the tool online was painful is because as time goes by, I made changes that the LLM wasn't aware of. That happened because I was using multiple LLMs windows in parallel and I also made code changes on my own. When a moment to debug something came, I had to repeatedly copy paste the code thing to enable the LLM to even try to fix it. **Whenever there is a problem, I need to paste in my best guess of the whole stack of where the problem is.** Sometimes, that is more complicated than writing a toy example where the code fails myself.

### Having better feedback loops is better programming

From the above, it seems that a big problem with the 'offline programming, online LLM' system is that the feedback loop is slow. The faster feedback loop would have the LLM live on top of the code so that there is no need for additional copy-pasting.

But, this idea of fast feedback loops seems to extend much beyond avoiding copy-pasting code. Everything that the code interacts with is a kind of a feedback loop to the programmer - the LLM as a coding assistant could try to automate the reasoning on top of every feedback loop.

Let's think of some feedback loops that we use, and that the LLM could also use:
- un/successful execution of tests - if the tests are fast enough, LLM could iterate on its own when writing code,
- fuzzy testing - the LLM might even be aware of which parts could be fuzzed and do that on its own,
- UI testing - if there is an interaction that causes the program to crash or not display something, the programmer knows that something is broken. This is harder to achieve than other points since the UI is not naturally text based, but with LLMs controling the operating system there might be some new ways of doing things...
### Browser UI design

A problem I had was why the script doesn't run twice within the browser. To debug, I made a simple version that tests if anything runs twice, and then made it more and more similar to the original script (the conclusion was that whenever I had a **let** or **class** keyword, things broke).

If we could somehow make the LLM aware of what is happening during web app testing, make it aware of the steps we are taking and what was the result, some new capabilities might be unlocked (or technology to do this already exists). To ahchieve that, we would need to give the LLM (or any agent for that matter) the **ability to interact with the browser, and make it aware of changes happening within it**. Some browser tools like Playwright do give the ability to interact, so the main problem is around describing the UI changes.

With the advent of multi-modal LLMs, we could bring a tank to a knife fight by only relying on the LLM visually understanding what's happening, but another possiblity is to have an abstracted language that describes what happens in the browser.
