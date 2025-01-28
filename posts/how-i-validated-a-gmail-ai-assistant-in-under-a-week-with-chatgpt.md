---
title: How I Validated a Gmail AI Assistant in Under a Week with ChatGPT
original: https://medium.com/@pol.avec/how-i-validated-a-gmail-ai-assistant-in-under-a-week-with-chatgpt-d33213d6fd0d
images:
- images/1*lht4LuNF5DK8yxxeBo05og.png
---

It was April 13th, and I was attending the [AACR Annual
meeting](https://www.aacr.org/meeting/aacr-annual-meeting-2023/), one of the
biggest cancer conferences in the world. We had a booth in the exhibition, so
we arrived a few days early to set up and ensure everything was in order.
During the downtime between talks and commutes, our CEO shared an idea that
changed everything.

“Wouldn’t it be great if I could just stop having to write so many emails?
That’s something I would pay for,” he said.

“That’s something I can build,” I replied.

“We shouldn’t build that if we can’t sell it,” he countered.

At this point, it’s worth noting that we are a biotech company specialising in
single-cell RNA sequencing analysis. As a computer scientist, building an AI
email assistant is not part of my typical workload. However, I had been going
on endlessly about how easy it is to build prototypes with ChatGPT, and I
thought, why not give it a try?

# The Challenge: Validate Market Demand Quickly

The challenge was to verify if there was enough market demand for our Gmail AI
assistant. As you’ve probably read elsewhere, your goal is to make sure
someone is willing to pay for your product without having to fully develop it
(I think I read that in [the Lean Startup](https://theleanstartup.com/)). The
most common way is to develop an initial version, called minimum viable
product (MVP), and try to sell it to see if anyone is actually interested.
This way you can validate your idea and getting early user feedback without
committing all the resources needed to build the full product. Since I had
some spare time at our booth, I decided to give it a go and take the first
step.

I began by deciding on the type of product I wanted to build and then tried to
quickly implement an MVP for the front-end part.

# 1\. Charting the Course: Deciding What to Build

As a newcomer to Gmail plugin development, I turned to ChatGPT for guidance.
Crafting effective prompts can be challenging, but my goal was to receive a
comprehensive list of options and then decide on the best approach. So, I
posed the following question:

> _> As the principal engineer of a new company, we want to offer an AI system
> that auto-generates replies to emails. We think it should be integrated into
> Gmail, but we’re unsure about the best technical option. Can you provide
> suggestions and evaluate them, considering we want something fast and simple
> for the demo and MVP?_

ChatGPT provided a thorough analysis, presenting the pros and cons of creating
a browser extension, a standalone web app, and a Gmail add-on.

When working with ChatGPT, it’s helpful to request high-level steps to
complete a task, allowing you to focus on one step at a time while maintaining
a relevant context. Keep in mind that ChatGPT may make assumptions that don’t
fit your project, so it’s essential to clarify them early on. In my case, I
asked for a recommendation based on ease of implementation, an outline of the
required work, and a description of the user interaction.

> _> Choose one of the options based on how easy it is to implement, and
> provide an outline of the work to do. Also, give a brief description of how
> the user would interact with the feature._

This prompt proved useful, as it included deciding which browser to use and
the back-end implementation in the work plan. Wanting to focus solely on the
front-end, I specified that we’d use Chrome and that it could assume that the
back-end interface would be a single endpoint. How to actually build such a
back-end I will tell you in a later post. With these details ironed out, we
advanced to the next phase.

# 2\. Crafting the Extension: Building and Testing

With a clear idea of what I needed to create, it was time to dive into the
actual development process. My prompt for ChatGPT was:

> _> We will use Google Chrome. Assume the back-end will be an endpoint where
> you do a post request with the email text and receive a text answer. The
> user interaction will consist of a single button that generates a complete
> reply, which users can then edit. Now let’s start coding — can you explain
> how you’ll develop the feature?_

ChatGPT responded with a comprehensive guide on implementing and testing the
browser extension. Here’s a brief rundown of the steps, excluding the actual
file contents:

  1. Create a new folder for the extension and set up the necessary files:   
a. `manifest.json`: Contains metadata about the extension, such as its name,
version, and permissions.  
b. `content_script.js`: Injects custom functionality into Gmail’s web
interface.  
c. `background.js`: Manages background tasks, like communication between the
content script and your back-end server.

  2. Define the extension’s metadata in the `manifest.json` file.
  3. Implement the content script in `content_script.js`.
  4. Implement the background script in `background.js`.
  5. Test the extension locally:   
a. Open Google Chrome and navigate to `chrome://extensions`.  
b. Enable “Developer mode” in the top-right corner.  
c. Click “Load unpacked” and select the folder containing your extension
files. The extension should now appear in the list and be enabled.

After requesting the contents of `popup.html` and `popup.js`, I put together
the files — `manifest.json`, `content_script.js`, `popup.html`, and `popup.js`
— and decided to try loading it into Chrome. To my surprise, it worked right
away. I had a working Chrome Extension in no time without even reading the
code! Really wow.

Up to this point browser extensions were something complicated that only
really purposeful and dedicated people (like[ Henrik Aasted
Sørensen](https://www.linkedin.com/in/aasted/?originalSubdomain=dk), the guy
from [Adblock Plus](https://help.adblockplus.org/hc/en-us), who made browsing
bearable again) would attempt, not lazy me. But that was all and in less than
half day I had a working Chrome extension (kudos to google also for how easy
to test this out in Chrome is).

Loading your own extension into Chrome without writing a single line of code
with ChatGPT.

**Full disclosure:** after getting the extension to work, I lost half a day
asking chatGPT to craft different CSS files for it in the style of
[Bitwarden](https://bitwarden.com/\)), [Material
Design](https://m3.material.io/), or [AdBlock
Plus](https://help.adblockplus.org/hc/en-us). It is so easy. So be ware of
getting distracted with the many random things ChatGPT can do for you if focus
is to quickly iterate.

# In Conclusion: Accelerating Development with ChatGPT

So there you have it! With ChatGPT on my side, I managed to create the first
version of my extension in just a day. It goes to show that turning an idea
into a product doesn’t have to be a big ordeal anymore (and many committed
weekends). With the right AI tools, like ChatGPT, you can bring your ideas to
life faster than ever before.

Now, my next move is to set up a basic landing page to showcase the product
and gather some precious feedback on its potential market demand. Keep an eye
out for my next post, where I’ll take you through the process of building the
website, adding Google sign-in, integrating Stripe payments, and sharing
useful tips on how to turn your own ideas into a reality. Whether the ideas
are good still depends on you ;)

And hey, don’t forget that the best is to keep iterating and improving. If
you’ve got any suggestions to make this post more interesting or engaging,
don’t be shy — let me know! and if you liked the product check out [R.Ai on
Product Hunt](https://www.producthunt.com/products/r-ai)!


