---

title: "Code Amnesia: The Hidden Side Effect of Developing with AI"

description: "AI can write code in seconds, but it can't remember why you accepted it. Why documenting decisions matters more than documenting code in the age of AI."

pubDatetime: 2026-07-01

tags:

- ai

- documentation

- software-engineering

- developer-productivity

- knowledge-management


featured: true

draft: false

hideEditPost: true

---

# Code Amnesia: The Side Effect of Developing with AI

A few years ago, you opened an old project and thought:

> *"Who on earth wrote this mess?"*

It was an uncomfortable feeling, but at least the answer was clear: **me**.

Now the answer is much worse.

> *"I think it was me... but I wouldn't bet on it."*

And that's one of the side effects of developing with AI that nobody talks about.

It's not that AI writes bad code. In many cases, it writes perfectly good code. The real problem is different: **it's making us forget the decisions we made while building our software.**

## We used to copy code. Now we copy reasoning.

For years, the classic joke was copying code from Stack Overflow without fully understanding it.

But even then, there was a process.

You read several answers.

You ignored some.

You adapted one.

You broke something.

You fixed it.

And somehow, during that process, you ended up understanding why the code worked.

With AI, that process almost disappears.

You type:

> *"Create an Express middleware to validate a JWT."*

AI replies.

Copy.

Paste.

Tests pass.

Commit.

Next task.

Three weeks later, you open that file again and realize that ChatGPT was the real author. You just approved the Pull Request.

AI has dramatically reduced the time between **"I need this"** and **"It works."**

Unfortunately, it has also reduced the time between **"I understand it"** and **"I think I understand it."**

## Documentation is no longer about explaining the code

I think this is where things are changing.

For years, we documented **what** a class, function, or module does.

Today, that is becoming less valuable.

Any developer can read the code. And if they can't, an AI can probably explain it in seconds.

What really gets lost is something else.

The context.

The decisions.

The trade-offs.

The questions that someone already answered so nobody has to answer them again.

Six months from now, you won't need to remember that a function sorts a list.

You'll need to remember things like:

- Why did we choose Redis instead of RabbitMQ?
- Why is this validation disabled?
- Why does this algorithm look more complicated than it should?
- What limitations did the AI-generated solution have?
- Which alternatives did we reject?

That's what is worth documenting.

Not the code.

The decision.

## The best comment doesn't explain *what*

I have a theory.

About 80% of comments that look like this...

```java
// Do not remove.
```

...actually mean this:

```java
// I broke this three times.
// I don't remember exactly why.
// I'm scared.
```

And honestly, the second comment tells you much more.

Not because it's technical.

Because it gives you context.

## Don't document the prompt. Document the reason.

Lately, I've seen many people recommend saving every prompt used to generate code.

I'm not convinced.

A prompt is just a snapshot of one moment.

What really matters is understanding **why that code became part of the project.**

I'd much rather find something like this:

```md
This implementation was generated with AI assistance.

We chose it because we needed a temporary solution during the authentication system migration.

We decided not to implement refresh tokens to reduce deployment risk.

Review this once the migration is complete.
```

That tells me much more than a twenty-line prompt.

Because the code will change.

The model will change.

The prompt will probably change too.

But the decision will still make sense.

## Bugs are documentation too

I started doing something a while ago.

If a bug costs me more than an hour, I write a note.

I don't save the stack trace.

I don't copy the full error message.

That's already in Git, the logs, or the issue tracker.

Instead, I write down the cause.

The wrong assumptions I made.

The ideas I ruled out.

The pattern I should recognize next time.

Because eight months from now, the error will probably be different.

But the cause will likely be the same.

And that note might save me another frustrating afternoon.

## The tool matters less than the habit

I use Obsidian because I can connect technical decisions, bugs, architecture, and project notes in one place.

But it could just as easily be a `docs/` folder, Notion, an internal wiki, or even a paper notebook if you're feeling optimistic.

The tool won't prevent code amnesia.

The habit will.

## AI doesn't replace documentation. It makes it more important.

For years, we believed documentation existed to explain the code to other developers.

I think that has changed.

Now it also exists to explain the code to the developer who approved it three months ago.

Because AI keeps getting better at writing code.

But it still won't be there when, a year from now, someone asks:

> *"Why was this done this way?"*

And that someone will probably be you.