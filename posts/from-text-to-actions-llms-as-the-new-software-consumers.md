---
title: "From text to actions: LLMs as the new software consumers"
original: https://medium.com/@pol.avec/from-text-to-actions-llms-as-the-new-software-consumers-8dc5fd065623
date: 2024-02-21
images:
- images/1*3Dr_rHMIRPYSeT4RZOL3xg.jpeg
---
# From text to actions: LLMs as the new software consumers

There are 3 main products using large language models (LLMs) nowadays. First, **question-answering** bots (QA bots) like [ChatGPT](https://chatgpt.com), which use LLMs to answer questions with text answers. Second, **Copilots** which are QA bots integrated into another tool. The integration with a tool allows them to actually perform some actions in addition to providing answers. And third, **workflow automation** systems that integrate multiple tools into the LLMs, as opposed to integrating the LLM into a single tool like in copilots.

While all of these solutions are already providing value, the main point of this post is that in the long run the most valuable product will be converting user requests to actions (not just answers), and that the only integration that matters is HTTP. We have built [Talk2Apis](https://github.com/kafkasl/talk2apis) as an example of what is already possible today.

Talk2Apis uses natural text to interact with any API through HTTP and it only needs the API definition to do so.

![AI agents acting in the market](https://raw.githubusercontent.com/kafkasl/medium2md/main/posts/images/text-to-action-market.jpeg)


So let's dive into the details.

The first contact the world had with LLMs was through ChatGPT, a QA bot where
you ask questions and receive text answers. Many more have appeared since then
like [Google's Gemini](https://gemini.google.com/app) or [Anthropic's
Claude](https://www.anthropic.com/news/introducing-claude). In these QA bots,
whenever you want an answer, you have to go to their website or app to use
them. This works fine for most people because they don't use it very often.
However, when you start to heavily use QA bots, having to move into the app or
the website every time starts creating friction due to the context switching.
In the case of coders, your day can become something like:

  1. Go to chatGPT
  2. Ask question
  3. Copy the code in the answer
  4. Go back to your editor
  5. Paste the code
  6. Repeat until the task is done.

It's a tremendous improvement, but after a while it starts becoming
bothersome.

To remove this friction, you can bring the bot into the tool the user is
working with. The resulting products are called**"copilots".** Their goal is
to remove or minimize the time you spend doing points 1, 3, 4, and 5. The best
example is [Github Copilot in VS
Code](https://code.visualstudio.com/docs/copilot/overview) (a code editor).
Having the LLM prompt in VS Code mostly removes the need to switch to another
window to ask the question while coding. Additionally it adds some nice
features like being able to look at the files you're working on minimizing the
copy-pasting to be done. However, it is still very much a QA bot with some
convenient features and less context switching.

Copilots remove the friction between **one tool** and the LLM, but what
happens if you frequently use multiple tools? You're back to square one, where
you have to keep context switching between different tools. The difference is
that now you'll probably have a different copilot in each tool. For example,
both VS Code and [Slack](https://slack.com/blog/news/why-we-built-the-chatgpt-
app-for-slack) have an LLM copilot now, but I still need to keep switching
between them many times a day.

Fixing this limitation brings us to **workflow automation** systems. Instead
of having one LLM agent inside your tool, the product is an LLM with all your
tools inside. Imagine that ChatGPT can access your email, code base, docs, and
slack and use those services in addition to providing any answers (a bit scary
yes, I know). If you are a coder those services might be where you spend most
of your day, so it would be a huge improvement.

There is not much being done in this space. Workflow automation does not fit
neatly into big companies' goals because it relies on integrating multiple
services (potentially from different companies) whereas companies, in general,
prefer you to spend more time on their own tools. Very good examples of this
are [Lutra AI](https://x.com/Lutra_AI) and
[Cognosys](https://x.com/cognosysai) which focus on converting questions into
workflows using code. They offer a range of integrations like Gmail, Slack or
Github. This is a great place to be and their products look amazing.

So now you have an LLM agent that can access and use your tools. But what
happens if tomorrow your company stops using slack and switches into
[Campfire](https://once.com/campfire)? If your workflow automation provider
already offers the Campfire integration, you will only have to create a new
account and set it up so your agent can use it (and cancel the slack
subscription). However, what if your provider does not have that integration?
Either you will be "vendor locked-in" so you won't use new tools because your
workflow automation provider does not support them. Or once again you will
have to start context switching, this time between the LLM agent that supports
most of your tools and the rest of unsupported tools.

In the future where tools are used mostly through LLMs, as a customer you
won't care about which tool is being used under-the-hood. The implication is
that tool developers might get more value by making the tools machine-friendly
than user-friendly. Maybe we'll end up with a new field called Machine
Interface/Machine Experience (MI/MX) in addition to UI/UX.

Luckily we already have very solid interactions between machines today and
they go over HTTP. In my opinion, HTTP is the only integration that matters.
Once LLM agents can use HTTP to interact with APIs, services, and websites,
they can do anything a human can do. For example,
[Mythra](https://mythra.vercel.app/) proposes to enrich websites with a text
file that allows LLMs to interpret natural language into HTTP requests to
embed interactive content directly in chats.

Our proposal, Talk2Apis, uses natural text to interact with APIs through HTTP
using only the API definition to do so. Adding a new service just requires
uploading the OpenAPI spec file describing the endpoints, the LLM handles the
rest. There's no need to code any new integrations to support new tools. To
add a new integration, you just upload the schema file. Period.

We believe this approach is the most powerful and more flexible in the long
run. It will probably shift the focus from developing user friendly tools, to
developing machine friendly tools. This also has the potential to transform
the software landscape into a [machine-first
economy](https://positiveblue.substack.com/p/building-for-the-next-million-
bitcoin)[.](https://positiveblue.substack.com/p/building-for-the-next-million-
bitcoin?utm_source=profile&utm_medium=reader2) Learn more about
Talk2Apis and what is already possible today so you
can get ahead of the curve and start building the future.

Join the discussion by giving feedback about [Talk2Apis](https://github.com/kafkasl/talk2apis)
and pushing LLMs further:

  * Reach us on X, [@pol_avec](https://x.com/pol_avec) & [@positiveblue2](https://x.com/positiveblue2)
  * Look at the code on [GitHub](https://github.com/kafkasl/talk2apis)

