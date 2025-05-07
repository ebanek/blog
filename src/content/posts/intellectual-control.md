How do we maximally leverage AI for development speed, while keeping our projects maintainable? A randomized control trial done at Google[^googlerct], showed that using a generic AI toolset made developers between 21% and 26% faster. Given that AI workflows can be very customizable, I think those measured values are the minimum of how much faster we can be.

Comparing it to vibe-coding, where AI creates end-to-end projects, ~25% feels like a small speed-up. The main problem with vibe-coding is that development speed on each project quickly goes to zero. I assume that's because the program becomes tightly wound up in complexity - there is no going forward without going back and changing something already done[^primedevin]. And going back and untangling takes more time than just rewriting things from scratch. For longer and bigger projects, it seems like it's still overall faster to take those ~25%.

Are there ways to take the speed of vibe-coding, but without quickly creating an unmaintainable mess? To understand this, it would make sense to look at past work which tries to understand why development speed in projects goes down. That kind of work often includes talking about tech debt. Here, I will mainly focus on the idea of intellectual control. I learned about this idea from George Fairbanks[^fairbanksic] while I was at Google, where he is one of the organizers of classes in software architecture. Recently, when I experimented with vibe-coding, intellectual control matches exactly what I feel is missing.

The idea is quite abstract, so things might get fuzzy. This is also a general problem with discussing more complex AI usecases - it takes time just to get up to speed. I would love to include more concrete cases, so if you want to chat about them or share them, please contact me!
# Intellectual control

For a software project, module, program, or a function, **control** is:
- the ability to use the program to satisfy real world or business goals,
- the ability to change the program to accomplish new goals which it currently doesn't satisfy.

**Intellectual control**[^fairbanksic] is when the control comes from the things that we have in our head - the accumulated knowledge about the project and the intuition of how the program works. If a person has intellectual control over a project they can probably:
- predict outputs for a given set of inputs,
- know what needs to happen if they want to fix a bug,
- give impromptu talks about the system,
- draw diagrams of the system which are relevant for the problem at hand,
- roughly predict how much work would a certain change be,
- teach others about the project.
It is not easy to gain intellectual control over a software project - it is a kind of tacit knowledge for that specific project.

Of course, there exist programs that no human could ever have intellectual control over. (I am not talking about machine learning - these are blackboxes from one standpoint, but we still have control through evaluations.) A spaghetti mess of modules and functionalities, might prove too complicated for any brain to understand[^redditlambdas]. In 1972, as if he was desperately advocating for something that probaly won't happen, Dijkstra[^dijkstramanageable] suggested restricting ourselves to **intellectually manageable programs**. To have programs over which we have intellectual control, we need to have programs that are sufficiently understandable.

%% this paragraph is just...ok maybe I should talk more about abstractions %%
Since our working memory is limited, we probably never have the model of the whole codebase in our heads. It is a continuum, not a boolean, as we have more intellectual control over some parts of the codebase, and less intellectual control over other parts. 
%%maybe I should talk more about abstractions %%Abstraction is the main system through which we can understand bigger parts at once.

One solution that Fairbanks presents[^fairbankspartner] for having manageable programs is **code as a partner for our thoughts**. Between the code and what we think, there could be a back-and-forth relationship. Similarly to how writing lets us think of ideas that we haven't even considered before, code helps us think more complicated thoughts and capture shortcuts to things we understood previously.
## Small scale control example

Here is an example of how types could help us have control:
```
def make_them_laugh(people, joke):

def make_them_laugh(people: List[Person], joke: str) -> bool:
```
Just from the type signature, we know more about the function! We don't need to go inside the implementation to learn what kind of objects does ```make_them_laugh``` accept. Intellectual control here is the knowledge about the function behavior. The function type lets us access the knowledge faster - and as our codebase grows, accessibility matters a lot.

In the above case, we might even infer that the return variable means some kind of success. To signify that, we could wrap the return variable into some kind of Enum or Class:
```
def make_them_laugh(people: List[Person], joke: str) -> AudienceResult:
```

The ```AudienceResult``` type might not bring any benefits regarding code execution or functionality, but it is beneficial to our understanding of what happens when we ```make_them_laugh``` - it is a shortcut for our brain.

 Other ways we could achieve control on a small scale could be related to naming, function structure, folder organization and decisions around libraries.
## Large scale control example

Some examples of intellectual control on a larger scale include:
- what should we do to simplify our program,
- how can we reuse the code that already exists to implement a particular feature,
- what kind of architecture would let us experiment quickly.

As an example, here is an excerpt from Architecture of Open Source Applications[^aosamatplotlib] where John Hunter and Michael Doettboom write about ```matplotlib```:

> Over time, the drawing API of the output backends grew a large number of methods (...). Unfortunately, having more backend methods meant it took much longer to write a new backend, and as new features were added to the core, updating the existing backends took considerable work.
> Since each of the backends was implemented by a single developer who was expert in a particular output file format, it sometimes took a long time for a new feature to arrive in all of the backends, causing confusion for the user about which features were available where.
> 
> For matplotlib version 0.98, the backends were refactored to require only the minimum necessary functionality in the backends themselves, with everything else moved into the core. The number of required methods in the backend API was reduced considerably (...)

The developers noticed a problem where doing a particular job started taking more and more time. By refactoring, they made the the job of adding new backend methods faster. If they didn't have intellectual control, knowledge of how adding backends interacts with the code and how that can be simplified, they wouldn't be able to improve it.  
# Statistical control

Did you ever need to change 'just one small thing' in an unfamiliar codebase? You search until you find the function implementing it, fiddle around with it and implement your change, and (if you're lucky to have tests) break several tests. Then you go into the test suite, play a bit there, and with some back and forth between the implementation and test you made your change with fully passing tests. Congratulations!

But, **did you do the right thing?** Will something else break? How do you know? Fairbanks named this **statistical control**[^fairbanksic]. You rely on tests to tell you whether you did something wrong. The statistical comes from the idea that tests only confirm that the specific inputs result in specific outputs - you're statistically not shipping a disaster. The one thing that is common in bugs caught by users is that the code passed all the tests[^hickeysimple].

When you have intellectual control, tests serve as a confirmation of your understanding of the code. They check that what you expect of the program behavior, really happens. With statistical control, you rely on tests as the source of truth.

## Why isn't statistical control enough?

Let's say that you make a purely test-driven change. If you are unfamiliar with the codebase, there might be another person who has intellectual control. They can review your change, and say whether it fits into the larger plan/idea of the codebase. But, what if there isn't such a person? For example, when you inherit a legacy codebase and the previous most senior engineer left.

One of your options is to become the person with intellectual control. As you continue shipping, you learn how the codebase works, and become an expert.

But what if the place is too big of a mess to tidy up your understanding? There is not much choice other than to continue pushing code based on tests and what you see in the code. As proof that this happens, we can rely on the popularity of the book *Working Effectively With Legacy Code* by Michael C. Feathers[^legacycode].

Let's fast forward the time working on such a codebase. This good enough to pass tests style of development results in tech-debt piling on. Since you don't have an overarching view of the project (i.e. intellectual control), each new change has its own logic, its own narrative. Soon, there are dependencies where there shouldn't be any. Each new change takes much more time than it should. You're left with a big ball of mud. A person modifying the code afterwards (might be you!) won't be able to tell why is a certain piece done the way it is, and will need to rediscover the logic. As the ball of mud grows, our development speed slowly but surely starts decreasing.

%%TODO: reproduce Fairbanks graph on SC+IC %%
# AI control

On the wings of a certain CEO saying that programming as a discipline will die out [^jensennobody], a lot of people have been talking about **vibe-coding**. For my purposes, I will define vibe-coding as programming using LLMs without knowing what code is being written. Let's call our ability to edit code through an LLM interface as **AI control**. Combining these, vibe-coding would be deciding to only rely on AI control for development, and forsaking intellectual control from the start.
## Analogy between AI control and outsourcing

In AI control we don't write the code. This is unlike the other forms of control - we never had a code generator of such high quality before.

But, using code we didn't write is not a new situation. There are several very common ways we rely on foreign code: operating system, programming language, libraries, APIs. For highly shared code, we trust it - we know that there is someone with high intellectual control working on that code.

We don't really trust the code written by LLMs. That's why AI control actually feels much more like having a junior or outscource engineer. In this case, the trust in the code hasn't been vetted as with highly shared code. Basically nobody else relied on it (it's bespoke for our usecase), tested it or tried it. The quality bar for usability is much lower by default. It's an unknown piece of code that should be carefully managed, and if done correctly, it can bring benefits.

I've also heard bad stories about code delivered by outsourcing agencies. Here is one from Bryan Clayton, the founder of GreenPal, talking about outsourcing on the podcast DevJourney: 
> And and first, he doesn't know what a lawn is, like he, he has never seen grass, he doesn't, he's never heard of a lawnmower and he doesn't know what a lawn is. And I'm like, I'm like damn man, like this dude. This dude is probably a really good engineer, but he has no, he has no context to understand what it is we're trying to actually solve in the world here.[^bryanclaytonoutsourcing]

There are some economic circumstances that make outsourcing not that good on average. But, Clayton above talks that even if the engineer hired was great, they were far removed from the problem being solved. Even if they did a good job, there are too many misunderstandings and issues that could happen because that engineer doesn't know the essence, the real world model, behind the problem.

(N.B. The founders ended up learning to code and built the product themselves.)

Joel Spolsky has a lovely essay [^spolskynih] on when you should build something in-house, vs when you can outsource the solution.

One extreme considered is the 'Not invented here' syndrome. This is when the team is reluctant to use any piece of code that they haven't developed in house. Such a team probably values intellectual control very highly. But, unless this team is full of prodigies, it is not very pragmatic to build everything in house. The other extreme is outsource everything.

Spolsky argues for the following heuristic: if it's a core business function, you must do it yourself. From a market competition standpoint, the things that give you an edge should be tightly controled, hence developed in-house.

## Why isn't AI control enough?

Here is a funny description by Reed Harper:

> When I describe this process to people I say “you have to aggressively keep track of what’s going on because you can easily get ahead of yourself." (...) For some reason I say “over my skis” a lot when talking about LLMs. I don’t know why. It resonates with me. Maybe it’s because it is beautiful smooth powder skiing, and then all of a sudden you are like “WHAT THE FUCK IS GOING ON!,” and are completely lost and suddenly fall off a cliff.[^reedworkflow]

In that same article, Harper shares a workflow for using LLMs in both greenfield and existing software projects. The above quote very aptly describes my experience when trying out vibe-coding. It starts out ok, but if I let go of the handles and try to generate more code than I can reasonably read, everything falls apart. A single bug causes an investigation into multiple code additions, and I often end up reverting commits to the last place where I understood what was happening. In the end, the efficiency gains are not as large as they initially seem.

This is exactly how it feels to **not have intellectual control** - but the main novelty is in the time-scale. Before, it wasn't possible to have no idea about what's happening so fast.

Short-term, it seems like a simple argument to make - of course you don't have intellectual control if you didn't write the code yourself. On the other hand, in large projects, the loss of control is not as obvious or expected. LLMs will get better at writing code, and since we want to reap the benefits of speed, we should think about the larger scale and how projects where humans wrote fewer lines of code look like.

Many people mention that their development speeds slows down to a crawl at some moment when vibe-coding, with the LLM tool not being able to modify the code anymore. This is the moment when AI control is lost - we lost the ability to effectively modify code in any way. It's reasonable to expect that LLMs will become better at coding. But we also want to have functional workflows that take into account the mistakes that they make today.

%% TODO: add 'VC money is invested to make this line longer' in below graph %%
<figure>
  <img src="speed_graph.png" alt="A grah with X axis labeled 1 day, 1 week, 1 month, 1 year and Y axis labeled speed. First curve shows that speed in case of AI control falls off after about a week. Second curves shows speed in case of statistical control, which is initially slower that AI control, but also falls down asymptotically to zero after multiple months. Third curve shows that intellectual control has the lowest initial speed, but it goes up as time passes."/>
  <figcaption>Engineering speed in relation to how old the project is. Intellectual control is the slowest initially, but doesn't end up in a state bogged down by accumulated complexity.</figcaption>
</figure>

Will we ever let LLMs completely take over the software engineering tasks? If we let AI control all of our coding, some decisions will be made for us. For the decisions AI makes for us, we cannot backtrack on them - because we are not aware of what is happening we don't have a choice. Even if we assume that AI control is good enough for our use-case, for every decision an LLM makes for us, we lose some edge that we could have made by choosing slightly differently. Continuing on Spolsky conclusion on what can be outsourced, it means that you want to be able to make decisions over the **core parts of your business**. If all code is outsourced to a commodity, it would mean that coding is not business critical and doesn't bring an edge.

The bigger the part you decide to 'outsource to LLMs', to bigger the possible discrepancy of your knowledge compared to what is inside the software.  Through us adding context about the world through code, that code becomes more relevant for our usecase. To squeeze every competitive edge as much as possible, there is no shortcut but understanding. To summarize the chain: AI control leads to us not making decisions, decisions mean competitive differentiation, and for core parts of business we want to have competitive differentiation. That means that we cannot let the core parts of the business be decided through AI control - we need to have understanding, i.e. intellectual control.
# Tools for intellectual control

%% Just read the code yourself %%

2. Capture decisions and trade-offs in Architecture Decision Records

Whenever you make trade-offs **document them**, because it will make your or another persons path to understanding easier than just reading code. Architecture Decision Records[^nygardadr] are documents which can capture these decisions. The idea is to have simply formatted text documents that live next to the code, where you often are when you make a decision. They should be tracked in version control, and can be reffered to from inside code comments.

3. With more risky code and AI tools, limit the blast radius

When you're using AI tools to speed up development, try to **limit the blast radius**. This is also why some AI tools recommend making step-by-step changes [^aidertips] - all with the goal of not falling off of your skis, of not losing the control you have. %%quote fairbanks%%

4. Design by contract

If the functionality is wrapped in a well designed contract and tested well, then the possible lack of knowledge is well-contained within the boundaries of that contract. You might even have no idea how it's implemented, but know how to handle that piece of functionality. Fairbanks made a course on design by contract[^fairbankscontract].

Here is a lightly edited example from something I worked on:

```
def get_outline_from_pixels(input_pixels: List[ImagePixel]) -> List[PixelVertex]:
    """
    Returns the vertices of pixels that form the outer outline of the input
    set of pixels.
    
    The first and last element are vertices of the same pixel.
    On inputs where the set of pixels is not a connected component, it will
    return the outline of the leftmost connected component of pixels.
    """
    # implementation
```

First, even if you don't know how this method ends up being implemented, the inputs and outputs form a contract which you can reason about. The contract tells you what you need to know to have a model of this single function. It is a leaf of the system

Second, this can serve as good context for prompting an LLM, as can the Architecture Decision Records. If we think that intellectual control is something we want from a system, then creating tools that work with our model of the problem should be a goal. We should center the AI tooling that we use around it as well as habitualize programming activities around it. If we don't create good artefacts that capture our model well, then we lack useful things to put as context into the AI.

5. Leverage the typing system and static analysis

We have powerful systems that can efficiently run tests, check that types are satisfied, check for index-out-of-bounds errors and so on. Leverage those to confirm your intuitions about the program.

# When to care about intellectual control?

## Speed over a long time

There is a correspondence - simple and theory building - contrasted with easy and vibe coding, where one results in fast speed over longer periods, and the other in fast initial speed. Hickey says that the fastest you can go is if you go on short tracks[^hickeysimple]. This means that because we need to invest additional effort to make sure our code can be worked on in the future, which happens less if we invest all our effort in delivering something fast. Ensuring intellectual control is one way in which we invest in the future.

## Other

1. Use two-way doors 

Leverage the concept of **two-way doors**[^bezosletter]. If you need to make a decision, and that decision is reversible, the best course is to just produce the code that would test out that hypothesis. Sometimes, taking action is faster than deliberating on which action would bring the best benefit[^chinaction]. If your code is written well, if you understand why code looks the way it is (for example through Architecture Decision Records), you can come back and decide differently.

2. It's easier to create with intellectual control, than to try to get it later

In the long run, be careful of having places with a large lack of intellectual control - it leads to quick creation of tech debt and a slowdown to development speed. The bigger scope of your project - in engineers working on it, people using it, computation done, the more relevant intellectual control is.

Previously, it didn't make sense to consider code that you don't understand from the start, so there was more emphasis on 'not losing' intellectual control. AI coding changed that, giving us a code generator - not the one that we need, but the one we deserve. If you produce a lot of code with AI where you don't know exactly what it does, treat it as carefully as it deserves, in order to not poison your whole system. Fairbanks pragmatically suggests to focus on code on the boundaries of your system (besides other places)[^fairbanksic].

3. Be aware when intellectual control is not your priority

A rough heuristic could be: the effort invested into a piece of software should be proportional to the number of times it is run.

The main problem with intellectual control is: it takes more time. In a way, intellectual control is exactly the investment we make into long-term longevity of our program - keep our ability to change things in it. If we could just have programs that are easily malleable over a long period, nobody would care about software engineering anymore. So it is exactly the trade-off between current velocity and future velocity, and the most easily maintainable software over a long run is the one that doesn't exist.

Not every company is like Google, which has a well-defined code review process and even a readability process. The incremental value of new code is low compared to a possible reputational risk, so the policies around code are enforced on everybody, even in places when they're not brining much value.

Maybe you're at an early startup in the explore phase, [^cohenexplore] where the goal is to fix every misunderstanding about the customer as fast as possible, and the product concept changes every day. There, it probably doesn't make sense to invest time in well-thought-out code. It would be a form of **premature optimization**, where you're optimizing your theories for a particular model of reality, but if that reality changes tomorrow, the work can be thrown away. The work of theory building in software is downstream of the business needs. If you expect that your model will change rapidly, then it might even make sense to let AI handle the complete development - if the scope of software you want to write is small enough for your velocity to not drop.

In addition, if something is not your core business software, as is often the case of an internal tool or a data result, AI is great for speeding it up. If you invested a small amount of effort in maintainability and documentation, and you see that it's used more than initially expected, you can revisit and invest more effort.

[^FairbanksIC]: [George Fairbanks: Intellectual Control](https://ieeexplore.ieee.org/document/8611447) ([conference talk]([talk](https://www.youtube.com/watch?v=Zs5UJqgu0tY)), [Testing Numbs Us to Our Loss of Intellectual Control](https://ieeexplore.ieee.org/document/9068304))
[^FairbanksPartner]: [George Fairbanks: Code Is Your Partner in Thought](https://www.computer.org/csdl/magazine/so/2020/05/09173629/1mts6mVaxDa) ([conference talk](https://www.youtube.com/watch?v=Eczq1ZplFGE))
[^FairbanksValue]: [George Fairbanks: Building Theories is Building Value](https://www.youtube.com/watch?v=KCrmquf9nPw)
[^FairbanksContract]: [George Fairbanks: Contract-based Design](https://www.youtube.com/playlist?list=PLRqKmfi2Jh3sd0K7bWHzgwOcee65Musgz) ([overview of the course](https://www.georgefairbanks.com/york-university-contract-based-design-2021))
[^HickeySimple]: [Rich Hickey: Simple Made Easy](https://github.com/matthiasn/talk-transcripts/blob/master/Hickey_Rich/SimpleMadeEasy.md)
[^NaurTheory]: [Peter Naur: Programming As Theory Building](https://pages.cs.wisc.edu/~remzi/Naur.pdf)
[^CunninghamDebt]: [Ward Cunningham: Debt Metaphor](https://www.youtube.com/watch?v=pqeJFYwnkjE)
[^JensenNobody]: [Jensen Huang: "It is our job to create computer technologies such that nobody has to program."](https://www.youtube.com/watch?v=8Pm2xEViNIo&t=1139s)
[^AiderTips]: https://aider.chat/docs/usage/tips.html
[^ReedWorkflow]: [Harper Reed: My LLM codegen workflow atm](https://harper.blog/2025/02/16/my-llm-codegen-workflow-atm/)
[^ChinAction]: [Cedric Chin: Action Produces Information](https://commoncog.com/action-produces-information/)
[^CohenExplore]: [Jason Cohen: Explore vs Execute](https://longform.asmartbear.com/explore-execute/)
[^SpolskyNih]: [Joel Spolsky: In Defense of Not-Invented-Here Syndrome](https://www.joelonsoftware.com/2001/10/14/in-defense-of-not-invented-here-syndrome/)
[^NygardAdr]: [Michael Nygard: Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
[^BryanClaytonOutsourcing]: https://devjourney.info/Guests/292-BryanClayton.html
[^BezosLetter]: [Jeff Bezos 1997 shareholder letter on Two-Way Doors (Invention Machine paragraph)](https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.htm)
[^GoogleRct]: [How much does AI impact development speed? An enterprise-based randomized controlled trial, Paradis et al.](https://arxiv.org/abs/2410.12944)
[^PrimeDevin]: For a funny example, you can check: [ThePrimeTime: Real Game Dev Reviews Game By Devin.ai](https://www.youtube.com/watch?v=NW6PhVdq9R8&t=6336s)
[^DijkstraManageable]: [Edsger W. Dijkstra: The Humble Programmer](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html)
[^RedditLambdas]: An example from r/ExperiencedDevs: [New company's backend is all lambdas? Am I crazy or is this a weird architecture](https://www.reddit.com/r/ExperiencedDevs/comments/1dkgkga/new_companys_backend_is_all_lambdas_am_i_crazy_or/)
[^aosamatplotlib]: [John Hunter and Michael Droettboom: matplotlib (The Architecture of Open Source Applications (Volume 2))](https://aosabook.org/en/v2/matplotlib.html)
[^legacycode]: [Michael C. Feathers: Working Effectively with Legacy Code](https://www.goodreads.com/book/show/44919.Working_Effectively_with_Legacy_Code)
