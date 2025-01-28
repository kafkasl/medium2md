---
title: Building apps no one needs: Dogfooding, Hammers and Over Engineering
original: https://medium.com/@pol.avec/building-apps-no-one-needs-dogfooding-hammers-and-over-engineering-5689c5e265c1
images:
- images/1*mjRPB7HiX7dsX_2ZzMOJFA.png
- images/1*x9KHym2NjNpINIXn1dlXpg.png
---

So, I had this brilliant idea for a platform. Spent weeks building it. Used
all the latest tech. Felt pretty clever about it, too. Only problem? Nobody
needed it. Not even me. Classic case of a solution in search of a problem.

It’s a common trap in tech: we fall in love with new tools and start seeing
problems everywhere that fit our shiny hammer. But there’s a difference
between tinkering and solving real issues. This project taught me valuable
lessons about dogfooding, over-engineering, and the importance of solving
actual problems. If you’re building something new, maybe my mistakes can save
you some time.

**The Trap of Solution-First Thinking**

Between jobs, I decided to learn some new ML tech. I took a course on
[Knowledge Graphs for RAG](https://www.deeplearning.ai/short-
courses/knowledge-graphs-rag/)* and decided to launch a product based on that.
Big mistake. Why? Because now I had a solution looking for a problem.

This is backwards. You don’t want to learn solutions. You want to solve
problems. When you have a nail to hit, you look for a hammer. I did the
opposite: I picked up a hammer, and suddenly everything looked like a nail.[1]

Fresh from my course, I was eager to apply knowledge graphs and LLMs. I
chatted with some friends in research. It seemed perfect: papers, authors, and
topics could all be modelled as a graph. We could run interesting queries that
traditional RAG struggles with. I bought the domain
[mypapers.ai](https://mypapers.ai) and started working on the idea.

But here’s the catch: I wasn’t doing research myself. I thought it might be
useful for other people. This was my second mistake. I wasn’t eating my own
dog food.

Startups often talk about “dogfooding” [2] — using your own product. It’s
critical. If you’re not solving your own problem, how do you know what to
focus on? How do you stay motivated?

I started coding anyway. I found some “interesting queries” to try, like
comparing AlphaStar and AlphaGo algorithms. But once I got the answer, my life
wasn’t any better. That should have been a red flag.

Here’s a rule of thumb: if you really want a problem solved, you’ll check for
existing solutions first. I found some good graph solutions out there. But
since it wasn’t my problem, I couldn’t tell if they were good enough. Worse, I
felt disappointed that someone had already done it.

That’s a dead giveaway. If you’re disappointed to find a solution, you don’t
really want to solve the problem. You want to build something cool. There’s
nothing wrong with that, but be honest with yourself about it.

I plodded on, convincing myself I’d found edge cases my solution could handle
better. But without solving my own problem, I had no North Star. My motivation
waned, especially as I started a new job.

Eventually, I scaled back. Instead of an amazing LLM & graph-based exploration
tool, I’d just plot daily papers as a graph with their references. Maybe a
journalist would find it useful. At worst, I’d finish and learn something.

This is where it gets interesting. My initial solution was a complex graph of
papers, authors, and chunks (parts of papers for LLMs to retrieve). For each
paper, I added:

  * A paper node
  * Multiple paper chunks
  * A node for each author

After a few days, I hit a wall. Adding data to the graph became painfully
slow. Over an hour per paper, and getting worse. Unsustainable.

I dug into Neo4j details and found a telling Reddit comment:

> “Performance isn’t great until you have the kind of graphy problems it
> excels at and a lot (A LOT!!!) of data.” [3]

Cold sweat. The ghosts of over-engineering and solutions-seeking-problems were
haunting me.

With the project on life support, I stripped it down. What was I actually
showing in the UI? Just papers with references. No RAG, no LLMs, no author
nodes. Just papers, colored by date.

I laughed out loud. I’d spent ages setting up Neo4j, learning it, deploying
it, fighting configs. And now? A simple SQLite database would have been
better, easier, and faster.

Here’s the kicker: my entire project stemmed from learning cool graph stuff
with LLMs. But the solution I ended up with — for a problem I didn’t even have
— would have been simpler with tools I already knew.

Yes, I learned a lot. But as product development? Total failure.

The lessons?

  1. Solve real problems, preferably your own.
  2. Be wary of solutions in search of problems.
  3. If you’re disappointed to find existing solutions, check your motivations.
  4. Start simple. Use tools you know.
  5. Only reach for new tech when you hit the limits of what you have.

There’s nothing wrong with learning for its own sake. In fact, it’s essential.
But be honest about your intentions. When you’re just tinkering and learning,
embrace that freedom to explore without restrictions. Don’t pretend you’re
building a product when you’re really just playing with new toys. Mixing these
approaches is a recipe for frustration.

Building a product requires focus, deadlines, and practical constraints.
Tinkering thrives on open-ended curiosity. They’re both valuable, but they
don’t mix well. Try to keep them separate.

**Learning vs. Building: A Delicate Balance**

Applying new knowledge to real-world problems is a crucial skill that improves
with practice. But when starting out, it’s wise to separate learning projects
from product development.

This separation isn’t just about efficiency — it’s about mental clarity.
Learning thrives on exploration; product building demands focus on real user
problems.

The challenge? Not fooling yourself. That shiny new tech might seem
revolutionary, but often you’re just looking for an excuse to tinker.

When tempted by new tech, be clear: Is this for learning or building? If
learning, explore freely. If building, focus on solving real problems with
appropriate tools.

With time, we might learn to blend these more seamlessly. For now, personally,
I will keep them separate because most products fail, so I don’t want to
associate learning new stuff with failure outcomes. [4]


