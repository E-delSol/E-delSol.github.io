---
title: "Following the Evidence: Investigating an OBS Studio Startup Crash"

description: "A real Linux debugging investigation that shows how evidence, controlled experiments, and careful reasoning are more valuable than guessing when a system crashes."

pubDatetime: 2026-07-08

tags:
- linux
- debugging
- platform-engineering
- sre
- open-source

featured: true

draft: false

hideEditPost: true
---

# When a `Segmentation fault` reminded me why I enjoy debugging systems

Linux has a special way of reminding you that, underneath all the polished desktop environments and shiny applications, it is still an operating system that is perfectly happy to tell you this

`Segmentation fault (core dumped)`

And nothing else.

No friendly error dialog.

No application window.

Not even the courtesy of crashing after it has started.

OBS Studio simply refused to launch.

After working with Linux for years, I know my first reaction almost by heart. My brain immediately starts looking for someone to blame.

_"It must be NVIDIA."_

_"Maybe the last Manjaro update."_

_"Could be FFmpeg."_

_"What did I change yesterday?"_

It is a very human reaction.

It is also a terrible debugging strategy.

Systems do not care about our assumptions. They only care about what is actually happening. If the evidence points somewhere else, our favorite theory is worth absolutely nothing.

So I tried to do something that is much harder than it sounds.

I stopped looking for a fix.

Instead, I started looking for evidence.

That sounds obvious. It rarely is.

The internet is full of commands that promise to fix almost any Linux problem. Some of them even work. The problem is that a fix without understanding is usually temporary. The next update breaks everything again and you are back where you started, except now you also have a mysterious command in your shell history that nobody remembers adding.

This time I wanted to understand the problem before trying to solve it.

So I collected everything that could tell me what was happening.

OBS logs.

Package versions.

Graphics information.

A GDB backtrace.

Anything that could give me facts before I started changing the system. Once you begin removing packages or editing configurations, every new result becomes harder to trust.

The first observations were interesting.

OBS stopped logging very early during startup.

OpenGL initialized correctly.

Safe Mode crashed exactly the same way.

That was already useful.

OpenGL working meant the graphics stack was not completely broken.

Safe Mode failing as well suggested that user configuration was probably not the problem.

None of those observations explained the crash.

They simply removed a few wrong ideas.

Sometimes that is how debugging moves forward.

The investigation became much more interesting when I looked at the backtrace.

Until then I only knew that OBS crashed.

Now I could see where it crashed.

The execution path ended while OBS was loading FFmpeg modules and initializing VAAPI.

At that point it was very tempting to say that VAAPI was the problem.

That would have been a mistake.

The backtrace did not identify the root cause.

It only showed the last observed execution path before the process received a segmentation fault.

That difference matters.

Observations are not conclusions.

With that in mind, I designed the first experiment.

If the crash happened during VAAPI initialization, what would happen if I prevented that initialization?

Not because I expected to fix the issue.

Because a good experiment should teach you something no matter what the result is.

I forced libva to use the dummy driver.

OBS started normally.

The interesting part was not that it launched.

The interesting part was what happened next.

NVENC still initialized successfully.

VAAPI failed gracefully.

There was no segmentation fault.

That was a useful observation.

It was not proof of anything.

A single experiment is never enough.

One of the easiest mistakes in debugging is falling in love with the first explanation that seems to fit.

So I tried something different.

I removed `libva-vdpau-driver`.

The result was exactly the same.

OBS started.

NVENC still worked.

VAAPI was unavailable.

The crash disappeared.

Two different experiments produced the same behavior.

That made the working hypothesis stronger.

It still did not identify the root cause.

When the investigation finished, I had fewer answers than I would have liked.

I still cannot say which component contains the actual defect.

The available evidence does not support that conclusion.

What I can say is much simpler.

The available evidence suggests that the crash happens while OBS probes VAAPI capabilities during startup under the tested conditions.

Avoiding that initialization path allows OBS to continue starting normally.

That is what the investigation shows.

Nothing more.

Some people may see that as an incomplete answer.

I see it differently.

Good debugging is not about guessing correctly.

It is about reducing uncertainty.

Every observation removes a possibility.

Every experiment tests a hypothesis.

Every result makes the next question a little better.

The complete investigation is available in the GitHub repository, including logs, backtraces, environment details, experiments, and reproducible results.

This article is not meant to replace that documentation.

It tells the story behind it.

Because that is the part I enjoy the most.

Not finding someone to blame.

Not collecting random fixes from old forum posts.

Just sitting in front of a complex system, letting the evidence speak first, and accepting that sometimes the most honest answer in engineering is simply

_"This is what the data tells us so far."_