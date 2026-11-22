---
title: "From Writing Code to Auditing What We Build. How AI Has Changed My Approach to Software Development"

description: "AI has changed the way software is built, shifting the developer's role from writing code to orchestrating agents, reviewing their output, and ensuring quality. A personal reflection on multi-agent workflows, automated auditing, and why cybersecurity is becoming the next natural step in my professional journey."

pubDatetime: 2026-11-22

tags:
- artificial-intelligence
- software-development
- cybersecurity
- ai-agents
- code-auditing
- software-quality

featured: true

draft: false

hideEditPost: true
---

# From Writing Code to Auditing What We Build. How AI Has Changed the Way I Understand Software Development

*AI has changed my work. My need to make things more complicated than they need to be is still the same.*

There is something I have been thinking about for a while, and I feel it deserves a deeper look beyond the usual conversations about artificial intelligence, productivity and code generation.

I have always loved programming. A bit old-school, if you want to call it that. Sitting in front of an editor, thinking about a solution, struggling with a problem, trying different approaches and enjoying the process of building something from scratch.

Spending an entire afternoon trying to figure out why something was not working, going through documentation, reading answers on Stack Overflow and almost by accident discovering that someone had faced exactly the same problem several years earlier. And, of course, finding that one answer with three upvotes that ended up saving your day.

For many years, a big part of what I enjoyed about computing was exactly that. Turning an idea into something that works, understanding every part of an application and finding solutions to problems that sometimes seemed much harder than they actually were.

But the whole landscape has changed. And it has changed much faster than most of us probably expected.

## Code is no longer the main bottleneck

The arrival of large language models and their integration into development environments has completely changed the way we build software.

Like many developers, I started by using these tools as assistants. Asking questions, generating functions, fixing errors, writing documentation and handling small automation tasks.

However, it quickly became clear that their potential went far beyond having a very smart autocomplete tool.

The move towards agent-based development environments has changed the game.

Today, we can work with different specialized agents, split tasks, multiplex contexts, run processes in parallel and coordinate workflows where different models work together towards the same goal.

And this is already part of my daily workflow.

When I work on a feature or a project, it makes less and less sense to handle the whole process as a single, linear conversation with one model. I prefer to break the work down, separate responsibilities and organize different workflows that can run in parallel.

One agent can analyze the problem and understand the project context. Another can focus on implementation. Others can work on tests, review changes or look for potential issues from a security perspective.

Meanwhile, my job is to keep the bigger picture in mind, coordinate these tasks, manage dependencies, compare results and decide what is ready to be integrated and what needs another review.

We are no longer just asking an AI to write a function. We can define a task, split it into smaller responsibilities, delegate analysis and implementation, run tests, request reviews and compare the results.

The developer is starting to take on a different role. Designing, directing, coordinating, supervising and making decisions about a process where they no longer necessarily write every line of code.

And this has a very important consequence.

Our ability to generate software has grown enormously, but our ability to understand, review and guarantee the quality of everything we produce has not necessarily grown at the same pace.

The bottleneck is moving.

## Orchestrating agents does not mean being in control

Working with multiple agents is fascinating, especially when you start building workflows where each one has a specific responsibility.

One agent can handle the initial analysis, another can implement a feature, another can prepare the tests and another can review the result from a different perspective.

Multiplexing tasks and contexts allows us to work in parallel and make much better use of the available capabilities.

But there is something I consider essential.

Having several agents working together does not automatically mean the result will be better.

We can have one agent implementing a feature, another reviewing it and a third checking its tests. But if they all share the same wrong assumptions, we may end up with three agents confirming the same mistake.

Independent reviews, good context, clearly defined responsibilities and the ability to compare results are all essential.

Simply adding more agents is not enough. We need to design the system that coordinates them properly.

Above all, we need mechanisms to verify their results instead of simply trusting what they tell us.

## The real challenge starts after the code is generated

This is probably the part that is having the biggest impact on the way I work today.

For a long time, the main effort in software development was turning requirements into working code. Designing a solution, implementing it, fixing errors and making everything work together.

Now we can speed up a large part of that process considerably.

But software still needs testing, maintenance, security, architectural consistency and validation.

In fact, the easier it becomes to produce code, the more important it is to have mechanisms that help us decide whether that code is actually good enough to accept.

For me, this is where much of the interesting work begins.

I am increasingly focused on automating review and auditing processes.

I am not just talking about running static analysis or executing a test suite. I mean building validation workflows that can review different aspects of an application.

- Static analysis and detection of problematic code patterns.
- Identifying vulnerabilities and bad practices.
- Reviewing dependencies and third-party components.
- Detecting exposed secrets and insecure configurations.
- Validating requirements and functional behaviour.
- Reviewing architecture and consistency between components.
- Automated audits to compare what has actually been implemented with what was originally expected.

I am also particularly interested in integrating these checks into multi-agent workflows, so that reviewing code is not just a final step at the end of development, but a core part of the whole process.

There is something we should not forget, though.

An agent reviewing code generated by another agent is not a replacement for an independent audit. Models can share the same limitations, miss the same issues or even reinforce incorrect conclusions.

That is why I believe we need to combine different validation methods. Automated tests, static analysis, specialized security tools, independent checks and human review whenever the situation requires it.

AI can help us review much more, but it does not remove our responsibility for what we put into production.

## When building software becomes too easy

There is another, more personal side to this whole evolution.

I still enjoy programming. I still like building applications, solving problems and experimenting with new technologies.

But I have to admit that how easily we can now turn an idea into something real has changed my relationship with the process.

In the past, I could spend hours trying to solve a problem and feel a huge sense of satisfaction when everything finally worked.

Now I can describe the same problem, orchestrate several agents and get a working solution in a fraction of that time.

From a productivity point of view, that is fantastic. But it also changes the experience.

When a large part of the implementation no longer depends directly on you, your relationship with the code becomes different. Even when you design the solution, direct the agents, review the results and make the decisions, you no longer experience the building process in quite the same way.

I do not think this is necessarily a bad thing. And I certainly do not think programming has lost its value.

It is simply that the satisfaction I used to find in certain parts of development is no longer quite the same.

Maybe because now I build more out of necessity, to solve specific problems or create value, rather than for the simple pleasure of creating something just because I can.

And I think it is important to recognize that without making it sound dramatic.

I have not lost my desire to learn or build things. My curiosity is simply starting to take me towards different kinds of problems.

## Why cybersecurity is starting to make so much sense

Lately, I have been moving more and more towards cybersecurity as part of my professional development.

I do not see it as a break from software development, but rather as a natural continuation of everything I have learned over the years.

In fact, I believe my experience as a developer is one of the things I want to make the most of.

Understanding how an application is built, how its components communicate, how its data is managed and what architectural decisions are behind it gives you a very valuable perspective when it comes to analyzing its security.

Finding vulnerabilities also requires understanding how things work.

You need to be able to read code, understand behaviour, question design decisions, analyze dependencies and understand what can happen when someone uses a system in a way its developers never expected.

And this is where I find something that really attracts me.

Research, analysis, finding weaknesses, critical thinking and the need to understand what is really happening beneath an implementation that may look perfectly fine on the surface.

Maybe this is exactly the kind of challenge my mind is looking for now.

I do not want to stop building software. I want to go deeper into what it takes to build it properly and, more importantly, understand how it can fail.

## Old-school mindset, new tools

I am not against AI. Quite the opposite.

I have adapted, trained myself and continue to invest time and money in learning new ways to work with it. I believe ignoring this evolution would mean closing our eyes to a huge technological shift.

But I also do not think we need to abandon everything that has made us enjoy computing for so many years.

The ability to solve problems, understand systems, analyze code and question solutions is still essential. Perhaps now more than ever.

AI can generate code, coordinate tasks and speed up processes. We still have to decide what is worth building, how we want to build it and what guarantees we need before trusting the result.

And personally, that last part is becoming more and more interesting to me.

I still enjoy writing code, but now I also want to enjoy breaking it, auditing it and understanding how much we can really trust it.

In the end, I am still looking for the same thing I always have. Good problems to solve.

I just feel like changing the kind of problems I work on.