---
title: "The Backdoor That Came Back: Investigating a Second PrestaShop Compromise"

description: "Three months after a PrestaShop backdoor was removed, the shop was compromised again. A real-world investigation using file hashes, core comparison, and forensic evidence to uncover what the first cleanup missed."

pubDatetime: 2026-10-18

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

# The Backdoor That Came Back

There is a phrase I have heard for many years. It always makes me smile.

> *"We didn't touch anything."*

It usually shows up right before something interesting happens. Interesting in the worst sense of the word.

This time, it was true.

That was the problem.

---

## The shop went down. Again.

Three months earlier, a PrestaShop shop had started returning Cloudflare error 521 responses. What looked like a server connectivity problem turned out to be something else entirely: a compromised `index.php`, a collection of webshells and backdoors, and a cleanup operation carried out under the usual constraints of shared hosting.

No SSH. FTP access only. Database access restricted by an IP allowlist.

Not exactly the ideal environment for incident response.

We investigated, removed malicious files, and got the shop back.

Then October arrived.

The shop was down again, but this time there was no familiar 521 error to point us in a direction. Instead, we had a blank page and a strange pattern: every URL seemed to end up at `/index.php`, with HTTP 302 redirects preserving the original query string.

Something was wrong. The question was whether we were looking at a new problem or an old one wearing a different hat.

I had a suspicion.

I didn't have proof.

Not yet.

## First rule: don't start deleting things

When a site is compromised, the temptation is obvious. Find the suspicious files, delete them, restore whatever looks broken, and get the shop running again.

It's understandable. The business is losing sales. Someone is asking when the website will be back. Nobody is particularly interested in a beautiful forensic timeline while the checkout is dead.

But there's a catch.

If you delete everything suspicious before collecting evidence, you may also delete the information that tells you what happened.

So this time, before cleaning anything, I wanted a copy of what was actually on the server.

The entire site was downloaded over FTP.

No fancy forensic workstation. No live response toolkit. Just the tools available and a careful copy of the evidence before touching it.

The first things that stood out were a malicious-looking `.htaccess` file and PHP files that had no business being there.

The `.htaccess` contained WordPress-style rules in a PrestaShop installation. That alone wasn't proof of an intrusion, but it was a pretty good reason to investigate further.

And then there were the files.

Some looked suspicious. Others looked deliberately boring. One of them would turn out to be particularly important.

## Twenty thousand files and a checksum

The shop was running PrestaShop 1.7.6.4.

Rather than trying to decide whether every file looked legitimate, I downloaded the official release and compared it with the files on the server.

The reference archive contained 20,085 files.

The comparison was straightforward: calculate MD5 checksums and compare the installed files against the official release.

MD5 isn't the hash I would choose for every security purpose, but this wasn't a contest to find a cryptographic collision. It was a practical comparison against a trusted reference. The question was whether the files matched.

The result was useful.

The official core files matched, with one glaring exception: `index.php`.

The installed file was 73,640 bytes.

The clean version was 1,116 bytes.

That's not a subtle patch.

It also wasn't the whole story.

A comparison against an official release can tell you whether files shipped with that release have changed. It cannot tell you whether someone has added files that were never part of the release in the first place.

And this installation had plenty of those.

The core comparison was clean enough to narrow the search.

The extra files were where things got interesting.

I've included the comparison method and the relevant evidence in the [recovery repository](https://github.com/E-delSol/prestashop-backdoor-recovery), so the investigation doesn't have to rely on my memory or on screenshots of a terminal.

## The file manager with no password

Among the extra files was `wp_blog_footer.php`.

The name sounded like something from a WordPress installation. The shop wasn't running WordPress.

Inside was a Chinese-language PHP file manager.

It claimed to have password protection.

It didn't.

There was no effective password check protecting the file operations. The script provided the ability to browse files and perform actions such as reading, editing, uploading, renaming and changing permissions.

In other words, someone who could reach this script could potentially modify files on the server through a web browser.

Remember the infected `index.php`?

Remember the malicious `.htaccess`?

A file manager like this provides a plausible way to put those files back, or to change them again after a cleanup.

It doesn't tell us exactly who did it, or when. We can't reconstruct every action that happened on the server. But it gives us a credible explanation for how an attacker could retain the ability to modify the installation without needing FTP credentials every time.

This was the piece that changed how I understood the first incident.

The problem might not have been that the original backdoor survived in precisely the same form.

The problem was that the ability to bring it back may have survived.

That's a very different kind of cleanup failure.

## The files that remembered July

There was another clue.

I compared the suspicious files from October with the indicators of compromise collected during the July incident.

Eight files had exactly the same SHA-256 hashes as files from the earlier investigation.

Not similar names. Not similar code. Identical hashes.

That establishes that the files had identical contents, assuming the hashes were calculated correctly. Together with the matching patterns and the other evidence, it strongly supports a connection between the two incidents.

It doesn't identify the person behind them. A hash is not an attribution tool.

But it does make the idea of two completely unrelated incidents much harder to accept.

July had left fingerprints in October.

The earlier cleanup had removed malicious files and restored the shop, but it had not eliminated every malicious artifact. The new investigation exposed what that meant in practice: a site can look healthy again while the conditions for another compromise remain in place.

And that is the uncomfortable part.

A successful recovery is not necessarily a complete recovery.

## The difference between a clean core and a clean shop

There is an important distinction here.

The comparison showed that the official PrestaShop files were intact apart from the identified `index.php` anomaly. That was valuable evidence.

It did **not** mean the installation was clean.

Malicious files can sit outside the official distribution. They can hide in directories that look plausible, use names that don't attract attention, or encode their functionality in ways that evade simple signature scans.

One example was a file placed under `image/`.

That directory name looked ordinary enough. But PrestaShop uses `img/` for its standard image directory. The file was an encoded PHP webshell using `hex2bin` and `eval`.

It was exactly the kind of thing that a quick visual inspection could miss.

There is no magic command that can prove a compromised website is safe again. Checksums, malware signatures, file listings and log analysis all provide pieces of the picture. Each has limits.

The job is to combine those pieces without claiming more certainty than they provide.

This time, I wanted the evidence to be reproducible, so I documented the indicators, hashes and comparison method in the [GitHub repository](https://github.com/E-delSol/prestashop-backdoor-recovery).

Not because a repository makes an investigation infallible.

Because another person should be able to check the work.

## Shared hosting changes the game

It's easy to recommend a perfect incident-response process from the comfort of an environment where you have root access, SSH, snapshots, centralized logs and the freedom to isolate a server.

This wasn't that environment.

We had FTP. Database access was restricted by IP. And we had a shop that needed to work.

Those constraints matter. They don't make a compromise impossible to investigate, but they change what can realistically be collected and how.

They also make it tempting to focus on the visible symptom: restore the files, fix the redirects, get the shop online.

That may be enough to restore service.

It isn't necessarily enough to restore trust.

We had a few theories about how the attacker initially got in. The installed modules and the administrative surface were worth investigating, including the possibility of weaknesses in third-party modules or inadequate restrictions on the back office.

But a plausible entry point is not the same thing as a proven entry point. The available evidence didn't let us establish the initial vector with certainty.

I would rather leave that question open than turn a suspicion into a fact just because it makes the story tidier.

Incident response is full of gaps. The important thing is to label them honestly.

## Stop rebuilding on top of a compromised installation

At some point, the question stopped being how to clean the old shop.

It became whether we should keep trusting it at all.

The installation was running PrestaShop 1.7.6.4. We had evidence of repeated compromise, a file manager that could modify files remotely, and malicious artifacts beyond the official core.

We could keep inspecting and cleaning the existing tree. We could try to account for every suspicious file and every possible persistence mechanism.

Or we could stop treating that tree as a trustworthy foundation.

The safer path was a fresh installation of a supported PrestaShop version, followed by a controlled migration of the data that actually needed to survive.

That means the database and required media, after review. Not blindly copying the old application files, modules, overrides and configuration into the new installation and hoping the infection doesn't come along for the ride.

A migration is not just a file copy.

It's a decision about what you trust enough to carry forward.

The old shop had already given us a reason not to trust its filesystem.

We didn't need to preserve that uncertainty.

## The evidence belongs somewhere

One thing I wanted to avoid was turning this into another story where the conclusion is simply, "We found some malware and cleaned it up."

That doesn't help the next person who encounters the same problem.

So I published the technical material in a public repository:

[**PrestaShop Backdoor Recovery — GitHub**](https://github.com/E-delSol/prestashop-backdoor-recovery)

It contains the incident timeline, indicators of compromise, file hashes, core-comparison methodology and cleanup guidance.

The article tells the story. The repository lets you examine the evidence behind it.

Neither replaces a proper investigation of a particular server. The indicators are clues, not a universal guarantee that a shop is infected or clean. But documenting them gives other people something concrete to work with.

And it means the next investigation doesn't have to start entirely from zero.

## The backdoor that came back

In July, we had a compromised shop.

In October, we had a compromised shop again.

The second incident showed that the first recovery hadn't removed every malicious artifact. It also gave us a much clearer picture of how persistent access could have been maintained.

What it didn't give us was a complete account of every action the attacker took, or definitive proof of the initial entry point.

That's the nature of this kind of work. You don't always get a neat ending, a name, or a single line in a log that explains everything.

Sometimes you get eight matching hashes, a suspicious file manager, a modified `index.php`, and a much better understanding of what went wrong.

The first time, we were trying to get the shop back.

The second time, we had to ask a harder question: **what would it take to trust it again?**

I'm not sure this story is over. But at least now, the evidence is documented, the old installation is no longer the foundation for the recovery, and the next round doesn't have to begin with the same assumptions.

For now, the ping-pong match has paused.

I'm not going to call it a win just yet.
