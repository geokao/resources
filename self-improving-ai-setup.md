# Give Your AI a Way to Improve Itself

**Version 1.0 · Last updated August 2, 2026**

*by George Kao*

Most people improve their AI setup the same way I did for the first year: something goes wrong, you notice, you sigh, and you add a line to your instructions file. Then you forget. Three weeks later the same thing goes wrong, and you can't remember whether you already wrote a rule for it.

That approach depends on you noticing, and on you having the energy to act on it at the exact moment you're annoyed. Both of those run out.

What I have now instead is a set of scheduled agents that run at 3am, read my own past sessions, find the places where my AI and I wasted each other's time, and either fix them or leave me one question. I stopped being the maintainer. The system runs its own upkeep, and it tells me only when it needs my hands.

This is how it's built, and how you'd build your own.

---

## What "improve itself" actually means here

Everyone's AI keeps some kind of instructions file. Mine is large, because I've been adding to it for months. The new development is that I'm no longer its only editor.

Four agents run on their own schedules, and the architecture is two doors plus two specialists.

**The Sunday door only proposes.** It reads my last week of session transcripts, Claude's release notes, and outside research on working-with-AI patterns, then writes findings to a folder. It never edits anything. It also tags each finding by what kind of decision it is: a fact that's checkably false (a dead file path, a stale time), a judgment call, or anything outward-facing.

**The Thursday door applies, with a way back.** It rereads a rotating slice of my rules and applies changes it's confident about, including deletions — it's the only pass allowed to subtract. It also picks up Sunday's "checkably false" tags, re-runs each check itself rather than trusting the tag, and repairs the ones that verify. Judgment calls stay in the folder for me. Outward-facing anything waits for me, always.

**The two specialists stay separate on purpose.** One tunes my custom skills weekly, measured by which ones I actually invoke, and queues the result for my one manual upload. One monthly pass re-tests one capability my setup previously recorded as impossible, in case the vendor has since shipped it — and it drives a browser, which is why it doesn't share a run with anything else.

A daily check-in makes the other four safe: it reads what everything did overnight and puts anything needing me in one short list.

To answer the question I get most: no, none of the improvement agents are daily. Weekly is enough for improvement. Daily is only for *surfacing*.

## It used to be six agents, and my AI redesigned it to four

This system wasn't designed in one sitting. It accreted — one agent at a time, each solving a problem I'd just hit, until I had six improvement agents and a nagging sense of overlap.

So I asked my AI to review its own improvement system and build a better one. It ran three independent redesigns against each other — one arguing for a single unified agent, one for the two-door split, one for changing almost nothing — and a judge scored them.

The unified mega-agent lost, which surprised me. The judge's merge test is the design lesson I'd carry anywhere: **merge two agents only when they share both failure modes and state.** The two proposal agents merged because they read the same transcripts and feed the same folder. The two rule-editing agents merged because they hold the same authority and need the same revert machinery. But the browser-driving agent stayed on its own: a browser run dies differently than a file-editing run, and you don't want one death taking out both.

And the migration doesn't trust itself. The old agents stay running alongside the new merged ones until each new lane has been verified on a real overnight fire — and one specific failure (the same change applied twice by old and new) was designated in advance as the halt signal.

---

## The four rules that keep this from going wrong

An AI that edits its own instructions is a genuinely bad idea implemented carelessly. Four constraints make it safe.

### 1. Internal and reversible, or it escalates

An agent may edit a rule, a playbook, a ledger, a note. It may not send an email, publish a post, spend money, delete my data, or change a permission. Anything outward-facing stops and waits for me, every time, no exceptions and no judgment calls about whether this one seems fine.

The test is a single question: can I undo it by myself tomorrow? Importance never enters into it.

### 2. Snapshot before touching, always

Every agent that edits a rule copies the file first, to a dated folder, before changing a byte. When I say "revert that," the restore is one file copy, done in seconds.

I also keep my whole instructions directory under version control, so an autonomous edit is attributable by diff. Snapshots handle the panic case; the diff handles the "what exactly changed?" case. They're different needs.

### 3. A change I might want back gets one file, and that file nags

When an agent applies something I'd want to know about, it writes a single review file. The morning check-in surfaces that file every single day until I respond. Not once. Every day.

The one exception is the "checkably false" class: a dead file path or a stale timestamp gets repaired without a review file, because there's no judgment in it for me to review — but every such repair is still listed in that run's log, so the trail exists.

This is what I'd underweight if I were building it again. A revert window that expires unanswered is the same as no revert window. The nagging is deliberate.

When I do say "revert that," the reverted item goes on a permanent veto list. Nothing on that list gets proposed again without genuinely new evidence. Otherwise the agent that suggested it rediscovers the same reasoning next month and I re-decide the same thing forever.

### 4. Evidence, or it doesn't get written

The bar for a new rule is one incident from one session: what went wrong, what it cost, and the line that stops a repeat.

General advice never clears that bar. If I let it in, the file fills with plausible-sounding rules nobody has ever tested, and the tested ones get harder to see. Every rule of mine carries a note saying what went wrong to cause it.

---

## The two failure modes worth knowing about in advance

I've been running this since roughly June. Two failures caught me off guard.

**An unattended run can be confidently wrong about your environment.** A 3am agent once reported that I was logged into an account under the wrong identity, and that went out as my number one morning task. I had been logged in correctly the whole time. The agent's check had grabbed the wrong element off a page and reasoned impeccably from it.

So the check-in now verifies any claim about my own setup before relaying it. One direct look at the live state. If it can't verify, it says "the run reported this; I haven't confirmed it" instead of handing me an instruction.

**Silence is ambiguous, and that's where failures hide.** Some of these agents are supposed to produce nothing most weeks. A quiet week is a healthy week. Which means a dead agent and a healthy agent look identical from the outside.

The fix is that each agent whose normal output is nothing has to leave a heartbeat — one line, written last, so its presence proves the run reached the end. The morning check-in reads heartbeats, not outputs. An agent that ran and found nothing writes a line saying so. An agent that died writes nothing, and the missing line is the alarm.

I found this the hard way. Two agents failed invisibly on the same night, and the only reason anything was caught is that a completely separate check happened to notice the same problem from another angle. That was luck, not design.

---

## What I'd tell you to build first

Don't build four agents. Build the surfacing layer, then one agent.

**Start with the daily check-in.** One scheduled run, or even a habit of asking your AI "what happened overnight?" It reads whatever your automations did and gives you one list. Without this, every agent you add is one more place you have to remember to check — and eventually you'll stop checking.

**Then add one proposal-only agent.** Have it read your recent sessions, find the friction, and write suggestions to a file. Let it change nothing at all. Run it for a month and read what it produces. You'll learn very quickly whether its judgment is good enough to trust with edits, and you'll have lost nothing if it isn't.

**Only then let an agent apply changes** — with a snapshot, a review file that nags, and a veto list.

Build in that order — each step is only safe once the one before it exists. An agent that edits your rules without a surfacing layer leaves you discovering its work by accident, months later, wondering why your AI started behaving differently.

---

## What surprised me

I expected this to save time. It does, but not much — maybe an hour a month of rule-tending I'd otherwise do myself.

What changed is that lessons stopped evaporating. Before, a hard-won discovery lived in my head until I forgot it, and then I'd rediscover it in three weeks and be annoyed both times. Now the agent that was watching writes it down, in the place where it will fire the next time it's relevant.

Any single improvement is small. A year of them, none of which required me to remember anything, compounds into a different setup entirely.

---

*Questions or corrections are welcome. I'm at [georgekao.com](https://www.georgekao.com) and on X as [@georgekao](https://x.com/georgekao).*
