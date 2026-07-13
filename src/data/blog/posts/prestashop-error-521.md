---
title: "The 521 Error That Wasn't: Following the Evidence to a Backdoored PrestaShop"

description: "A real-world investigation into a PrestaShop outage where a Cloudflare 521 Error turned out to be a compromised index.php. A step-by-step debugging story focused on evidence instead of assumptions."

pubDatetime: 2026-07-13

tags:
- prestashop
- cybersecurity
- linux
- debugging
- php

featured: true

draft: false

hideEditPost: true
---

# The 521 Error That Wasn't Really a 521 Error

There is one sentence I have been hearing for years that always makes me smile.

> **"We didn't change anything."**

Funny enough, it usually comes right before one of the most ridiculous incidents you'll have to deal with that week.

This time, it was actually true.

An online store running **PrestaShop 1.7.6.4**, hosted on an **OVH Performance Hosting** plan, had been working perfectly for a long time. No recent updates. No new modules. No "I'll just try one quick thing" experiments that somehow end up stealing your entire weekend.

Then, one day, it simply stopped working.

The first symptom wasn't exactly original. Anyone trying to open the store was greeted by Cloudflare with a nice **Error 521**. In plain English, it basically means _"I tried to reach the server, but it completely ignored me."_

Nothing unusual... at least not yet.

The strange part came when I logged into the back office.

It worked.

No errors.

It felt like someone calmly telling you that the building is on fire while also mentioning that the elevator is still working perfectly.

That was the moment things started to feel wrong.

If the storefront is dead but the back office is still alive, the problem is no longer so obvious. Cloudflare was still a suspect. OVH was too. PHP or the database could also be causing trouble. But there was one piece that didn't fit.

If the server was completely down, why was I still able to access the admin panel?

As usually happens in situations like this, the first temptation is to start changing things. Upgrade PHP. Clear the cache. Restart services. Disable Cloudflare. Do anything that feels like progress.

I usually try to do the exact opposite.

When an incident appears without anyone changing anything, introducing new changes is a very effective way to make your life harder. If you change three things at once and the problem disappears, you'll never know which one actually fixed it. And if the problem is still there, now you have a new problem mixed with the old one.

So my first decision was not to fix anything.

My first decision was to understand what was happening.

There is one rule I try never to break before starting any investigation.

Make a backup.

It doesn't matter if you're convinced you're only going to open one file for a quick look. It doesn't matter if you've been managing Linux servers for twenty years. The day you decide that a backup isn't necessary because _"this will only take five minutes"_ is usually the same day you discover you've deleted something you really shouldn't have.

With the FTP backup running and the peace of mind that I could always go back if I made a mistake, I was finally ready to start the real investigation.

At that point, I still had no idea that the **521 Error** would end up being the least interesting part of the whole story.

---

## The next stop was pretty obvious.

The logs.

Or, as I like to call them, the place where the error you're looking for lives alongside another two hundred that someone decided to ignore six months ago because _"the website was still working."_

With PrestaShop, the first place to look is usually `var/logs/prod.log`, so that's where I started.

It didn't take long before I found some familiar faces.

```
Allowed memory size exhausted
```

A few lines later...

```
SQLSTATE[HY000] [2002] Connection refused
```

Great.

In less than a minute I already had a memory problem and a database problem.

It almost felt too easy.

The problem is that logs have a terrible memory... or an excellent one, depending on how you look at it.

Just because an error appears in a log doesn't mean it's related to the problem you're investigating. It only means that it happened at some point, and someone was kind enough to write it down.

So the next step was checking the timestamps.

And that's when the whole theory started to fall apart.

Those errors had been there for quite some time. They didn't match the moment when the store stopped working. They were just noise. Interesting noise, because an `Allowed memory size exhausted` error is never something you should ignore. But noise all the same.

I made a note to come back to them later and kept digging.

At this point, I still hadn't ruled anything out.

Cloudflare was still a suspect.

OVH too.

PHP was still sitting in the interrogation room.

And MySQL was waiting for its turn, looking completely innocent.

The next thing I tried was something fairly harmless.

Clearing the production cache.

Not because I thought it would magically fix everything. If you've worked with PrestaShop for a while, you know the cache can cause some pretty strange behavior when it becomes corrupted. Besides, deleting it is safe. If everything is working properly, PrestaShop simply rebuilds it on the next request.

So I deleted everything inside `var/cache/prod` and let PrestaShop do its thing.

I waited a few seconds.

Reloaded the page.

And then something happened that, instead of frustrating me, actually made me smile.

The **521 Error** was gone.

Now I was getting something different.

```
404 Not Found

nginx
```

It may sound strange to be happy about replacing one error with another.

But this was actually very good news.

Really good news.

A **521 Error** means Cloudflare can't even talk to the origin server.

A **404 from Nginx** means exactly the opposite.

The server is alive.

It's responding.

It's just responding with something it shouldn't.

It may look like a small difference, but during an investigation it's a huge one.

It was like walking into a completely dark room and realizing that, at least, someone had found the light switch.

I still couldn't see anything.

But now I knew there was electricity.

And that changed everything.

The list of suspects had just become much shorter.

The funny part was that I still had no idea just how much shorter it had become.

---

## With Cloudflare almost out of the picture, it was time to answer a simple question.

Was the problem really the server, or was it just PrestaShop?

The fastest way to find out was to forget about PrestaShop for a moment.

If the server couldn't even serve a simple HTML file, I could spend hours checking modules and configuration files, but the real problem would be much lower in the stack.

So I ran the simplest test possible.

I created a file called `test.html` with just a few lines of text. Nothing fancy. Just enough to see whether Nginx could serve a static file.

The first request ended exactly like the others.

A nice **404**.

I have to admit that, at that point, I started looking at OVH. It wouldn't have been the first time a hosting control panel said one thing while the server decided to do something completely different.

I checked the hosting configuration again.

The domain was still pointing to the correct directory.

The Multisite configuration looked exactly as expected.

Nothing had changed.

I tried again.

This time, `test.html` loaded perfectly.

I won't pretend that seeing four lines of text on a blank page was exciting. But after staring at nothing but error messages for a while, it almost felt like a small victory.

It was an important clue.

Nginx was serving static files without any problems.

So the next question was obvious.

**What about PHP?**

Another simple test.

A file called `info.php`.

Inside it, just an old friend that every PHP developer has used at least once.

```php
<?php
phpinfo();
```

I opened it in the browser.

There it was.

That endless purple table full of PHP information that, let's be honest, nobody has ever read from top to bottom.

Perfect.

PHP was working.

Nginx was executing PHP scripts correctly.

The connection between them was working exactly as it should.

By this point, I had already ruled out several theories.

It didn't look like a DNS problem.

It didn't look like a hosting problem.

It didn't look like an Nginx problem.

It didn't look like a PHP problem.

Everything was starting to point in the same direction.

The application itself.

Then I tried something I was already expecting to work.

Opening PrestaShop's main entry point directly.

```
https://mydomain/index.php
```

**404.**

Again.

That was the moment I found myself staring at the screen for a few seconds, thinking,

_"This makes absolutely no sense."_

I had just proved that PHP could execute code without any issues.

`phpinfo()` had worked perfectly.

So...

Why was the most important file in the whole application still returning a **404**?

It wasn't a syntax error.

It wasn't PHP.

It wasn't Nginx.

It was **that file**.

Or at least, everything was starting to point in that direction.

Until then, I had been trying to prove that the server was the problem.

Without realizing it, I had just proved the exact opposite.

And when an investigation manages to eliminate almost every suspect, there is only one thing left to do.

Open the file you've been avoiding all along.

---

## There are files you open without thinking.

And then there are files that, for some reason, make you hesitate for a second before you double-click them.

I can't really explain why, but `index.php` immediately fell into the second category.

I downloaded it over FTP and opened it, expecting to find almost anything... except what was waiting on the very first line.

```php
//Obfuscate by https://uutool.cn/php
```

I stared at it for a few seconds.

Not because I didn't understand what it said.

Quite the opposite.

I understood it perfectly.

And it had absolutely no business being there.

There are very few things that make me more suspicious than the word **"obfuscate"** inside a file that should be part of an application's core.

Obfuscated code does have legitimate uses. Some developers use it to protect commercial software or make reverse engineering more difficult. I've never been a big fan of the idea, but it exists and it's part of the software world.

The problem was that this wasn't a commercial module.

This was PrestaShop's `index.php`.

The file that handles almost every request to the store.

I took a deep breath and started scrolling.

What came next was not exactly pleasant to read.

Variables with impossible names.

Functions built at runtime.

Huge encoded strings.

`base64_decode`.

`goto`.

`curl` calls.

Code jumping all over the place as if it had been written by someone allergic to line breaks.

The more I read, the less I needed to understand the code itself.

I had seen enough backdoors over the years to recognize the pattern.

Experience works in funny ways.

When you're starting out, you try to understand every single line of code.

After dealing with enough incidents, you learn that some files don't need to be understood.

They only need to be recognized.

And this file was practically wearing a giant neon sign saying exactly what it was.

The most disturbing part came a few hundred lines later.

Suddenly, I found something familiar.

```php
require dirname(__FILE__).'/config/config.inc.php';

Dispatcher::getInstance()->dispatch();
```

There it was.

The real `index.php`.

The original PrestaShop code.

Completely untouched.

Waiting quietly at the end of the file.

That's when everything finally made sense.

The attacker hadn't replaced the application's entry point.

They had done something much smarter.

They had inserted their own code at the beginning of the file and then allowed PrestaShop to continue starting normally.

As long as nobody looked at the file, the store would keep working.

And the backdoor would stay there.

Silent.

Waiting.

That also answered the question that had been in the back of my mind since the beginning.

Why would a store that had been working perfectly for so long suddenly stop working?

Because the **521 Error** was never the real problem.

The real problem had been hiding there for much longer.

That day, it simply stopped hiding.

On paper, the fix was almost ridiculously simple.

Replace `index.php` with a clean copy.

After everything that had happened over the last few hours, it was hard to believe that the whole incident could come down to a single file.

But before replacing it, I spent a few more minutes looking at that obfuscated code.

Not because I was curious.

Because of something every system administrator learns sooner or later.

When you find one backdoor, the scary part isn't the file you're looking at.

The scary part is wondering how many more you haven't found yet.

---

## At that point, there wasn't much left to investigate.

It was time to find out if my theory was correct.

I restored a clean copy of `index.php`, replaced the compromised file, and refreshed the page.

The store came back.

No ceremony.

No congratulations.

No fireworks.

It simply loaded again as if it hadn't spent the last few hours keeping my brain busy.

That's one of the funny things about this job.

You can spend half a day chasing what looks like a huge problem, only to fix it by replacing a file that's just a few kilobytes in size.

The temptation at that moment is to close your laptop and call the incident solved.

That would be a mistake.

In fact, it would probably be the biggest mistake of the entire investigation.

Because backdoors rarely come alone.

If someone has managed to modify the main entry point of an application, the most important question is no longer how to bring the website back online.

The real question is much simpler.

**How did it get there?**

The answer wasn't inside `index.php`.

I had to look somewhere else.

Was it a known vulnerability in the installed version?

A vulnerable third-party module?

Leaked FTP credentials?

A reused password?

Permissions that were too open?

An old user account everyone had forgotten about?

All of those were possible.

And all of them deserved to be investigated.

Replacing the file brought the website back.

It didn't guarantee that the attacker had lost access.

Once the store was working again, the less exciting—but much more important—part of the job began.

Reviewing the rest of the critical files.

Comparing them against a clean PrestaShop installation.

Looking for other recently modified PHP files.

Checking installed modules.

Changing passwords.

Reviewing user accounts.

Checking file permissions.

In other words, assuming that if you've found one cockroach in the kitchen, it's probably not the only one.

I didn't find any evidence that other core files had been modified, but that doesn't mean the risk magically disappeared. Whenever you deal with a compromise, it's a good idea to be suspicious even of the things that seem to be working normally.

Looking back, the **521 Error** was almost irrelevant.

It was the first visible symptom.

But it was also a perfect distraction.

For a good part of the investigation I was looking at Cloudflare, Nginx, PHP, and MySQL.

They all looked guilty.

In the end, they were all innocent.

The real culprit was sitting inside the very first file executed by the application.

Quietly waiting for someone to open it.

If there is one lesson I learned from this incident, it would probably be this.

**Servers can be misleading.**

Not because they lie on purpose.

They simply show you the first symptom they find.

And that symptom doesn't always point to the real problem.

A **521 Error** can hide a backdoor.

A memory error may have been sitting in the logs for months without being related to the current incident.

A `Connection refused` message may simply be the remains of a problem that happened weeks ago.

That's why I try to approach every incident the same way.

First, I try to prove that my theory is correct.

If I can't, I try to prove that it's wrong.

Once you've ruled out enough possibilities, the real answer usually reveals itself.

Not always quickly.

Not always elegantly.

And almost never where you expected to find it.

There is one last thought that comes to mind every time I finish dealing with an incident like this.

The obfuscated code wasn't what worried me the most.

Neither was the **521 Error**.

Not even discovering that `index.php` had been modified.

What really worries me is the false sense of security that appears as soon as the website starts working again.

Because once the site is back online, it's very easy to think the job is finished.

In reality, that's usually the moment when the real work begins.

In security, restoring the service usually means only one thing.

**Now you can finally start the real investigation.**