---
title: 'Code generation lessons from using Claude for a tiny Firefox extension'
description: 'I made a small Firefox extension within 1 hours using an LLM - these some ideas that came to mind about generating code'
pubDate: 2024-11-10
lastUpdateDate: 2024-11-10
---

I've created a tiny Firefox extension by only prompting Claude.
Before, I've directly prompted LLMs for making software around 10 times and found it useful around 3 times, mostly because what I wanted was too complex. Recently, I got interested in code generation and decided to generate an extension for speeding up a part of my workflow. The challenge was to have a working extension as fast as possible. 

Before prompting, I only did a quick search to verify that the Internet Archive supports my wishes. Next, I [asked Claude](https://pastebin.com/P0LgSGat) to make the extension for me. The [Firefox extension](https://addons.mozilla.org/en-US/firefox/addon/dateifier/) and the code is on [Github](https://github.com/ebanek/dateifier/tree/main). I've never made a Firefox extension before, and only did basic scripts in JavaScript.

This write-up has two goals:
- to record the experience while it's fresh,
- to make some predictions about code generation.

### What does the Firefox extension do?

When I investigate different blogs, products and landing pages, I often use the Internet Archive to check how old a page is. To save myself 10+ seconds each time, I decided to make an extension that streamlines that process. The extension makes a query to the Internet Archive and shows the list of current page snapshots that were made in archive - the first ever snapshot is a proxy for how old a page is.

### Main takeaways around code generation

My main takeaways from this experiment are:
- code-gen makes mistakes,
- code-gen tools should work better with more feedback loops,
- code-gen saves time, but sacrifices understanding gained.

### LLMs make mistakes

My initial prompt had a lot of context - probably more than a non-developer would know to add. In spite of that there were at least 3 mistakes (counting the ones that I could verify). For each mistake, I sent another prompt, which fixed that particular mistake.

The mistakes were:
- it started with chrome.tabs instead of browser.tabs - only the second version works in Firefox,
- it didn't correctly parse the JSON event with detailed instructions,
- the display of links was broken due to wrong parsing of the timestamp.

From the perspective of a non-developer, all 3 mistakes might be deal-breaking. The first two could maybe be solved with a better model. The third one is not easy to solve without feedback - the specific 'timestamp' could have many different conceivable formats. Any reasonable developer would check how the data looks like before finalizing the implementation.

In my previous experience, whenever we increase complexity, the amount of mistakes in generated code increases. It's not impossible that at some point LLMs (or other AI tools; hello AGI) stop making coding mistakes. But even then it's still possible that it doesn't align with what we actually wanted.

### Feedback loops with as many parts of the environment as possible

While figuring out how to deploy an extension in Firefox, I changed one of the files names and unknowingly broke the generated code. The LLM cannot know what to fix if it isn't aware of my workspace - I was prompting it through the online interface. I also didn't know that there is a reference to the .js file, and only until I figured that out did things start working. Proper code-generation is much easier to do if the LLM is integrated with the coding workspace - we can also see that from many startups releasing VSCode extension.

There was another example in favor of many feedback loops - I learned that the timestamps weren't parsed correctly when I saw the 'Invalid format' element in the display of each link. This 'Invalid format' is a signal on what is wrong, both to a human and a machine.
### It's faster, but I learned less

My goal for this experiment was speed - the whole thing was sent to Firefox for review under 1 hour. I only have a vague idea how to actually send an HTTP request in JavaScript - I've learned at least 50% less than if I wrote the code myself. Just typing the code by hand instead copy-pasting would've been a better learning experience.

Going deep on what's possible has always been a valuable skill to me - from debugging remote machines to designing bigger systems. But there is a constant trade-off between getting something that seems to work vs. learning things in-depth. In big tech, seniority often correlates with how effectively somebody can leverage the work of others around them, and seniors don't necessarily know everything in depth. Will there be a new brand of seniors that became seniors by only shipping LLM-assisted code? Probably yes, but they will have to have a great feeling on where to go deep, and where to just let things be as they are.

To channel my inner Byrne Hobart, we could say that using LLMs for coding we sacrifice optionality that learning brings us for speed of delivering results. Learning something in-depth allows us to do something differently (or better and faster) in the future.

### Please donate to the Internet Archive

Being the internet's hippocampus is an amazing mission. [Here is the donate link](https://archive.org/donate).
