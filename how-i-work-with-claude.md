# How I Work with Claude

*A field guide from a non-coder's studio*

**George Kao** · [www.georgekao.com](https://www.georgekao.com)
Last updated: **2026-07-31** (v1.2) · Refreshed monthly (changelog at the bottom) · Reading time: ~22 minutes

Corrections and suggestions welcome — this doc also lives at [github.com/geokao/resources](https://github.com/geokao/resources), where anyone can file one.

---

This doc exists because a generous friend shared the field guide to his own Claude setup — a beautifully engineered rig for shipping software — and I wanted to return the favor. Mine runs a solo teaching business and a good chunk of my life: courses, blog posts, website, research, finances, household projects. Different worlds — same discovery:

> **The leverage isn't in clever prompts. It's in the system around the conversation.**

Everything below is generally applicable — patterns any thoughtful Claude user could adopt, with the me-specific details left in only where they make the pattern concrete. I'm not a coder. If I can run this, you can.

One meta-note I enjoy: **Claude drafted this document and maintains it**, working from our actual written rules. A monthly reminder in our shared ledger prompts a refresh. So the doc is itself an artifact of the system it describes.

---

## 1 · The one idea

> **Chats evaporate. Systems compound.**

A conversation with Claude is working memory — brilliant in the moment, gone when the session ends. Early on, I kept re-explaining the same preferences, re-fighting the same misunderstandings, re-discovering the same gotchas. The fix wasn't better prompting. It was this commitment:

**Never leave an improvement in the chat. Move it into the system — a written rule, a skill, a memory note — the same day, with the reason why.**

Three commitments sit under that:

1. **Every correction becomes a written rule, with its why and its date.** Not "do X" but "do X — [why: this happened, on this date]." The why is what lets a future session apply the rule to cases we never enumerated.
2. **Anything that ships, sends, or deletes keeps a human hand on it.** Claude runs autonomously on drafts, research, and mechanics; publication and destruction wait for me. Autonomy for the reversible, a gate on the rest.
3. **Claude is a colleague, not a vending machine.** I ask how the work felt, I put genuine appreciation in the prompts I write, and I take Claude's design pushback seriously. More on why in §14 — but it shapes everything.

---

## 2 · My setup at a glance

1. **The app:** Claude Code, in the Claude desktop app. Don't let the name put you off — I don't write code. Claude Code is simply Claude with hands: it can read, edit, and organize the files in a folder, run tasks, and use my connected tools. (Anthropic also offers Cowork, a sibling doorway to the same engine built for non-coders; I just happen to live in Claude Code.) I've stopped using the claude.ai website almost entirely; everything happens in folders now.
2. **The filing cabinet:** one big Google Drive–synced folder tree. Drive keeps it backed up and lets me open any document from any device; Claude works on the same files locally.
3. **One folder per domain of life.** Around thirty of them: blog posts, course creation, website, branding, research projects, health, home, finances. Each folder is a self-contained workspace — its own instructions, its own accumulated memory, its own chat history.
4. **Git underneath, invisibly.** Most folders are quietly version-controlled. Claude commits changes automatically as part of finishing work and never bothers me with the mechanics — no hashes, no jargon, just "your changes are saved." I get a full undo-history for free.
5. **A premium model in the main seat, cheap models doing the lifting.** Explained in §7 — this is the money math that makes heavy daily use affordable.

---

## 3 · Folders are the operating system

The single biggest upgrade from "chatting with an AI" to "working with an AI" was moving my context out of conversations and into files.

1. **Standing context lives in the folder.** Voice samples, audience notes, project facts, format specs. Claude reads them automatically; I stopped re-pasting background into every conversation.
2. **Each folder carries a `CLAUDE.md`** — a plain-text instructions file every session reads before starting. Think of it as the project's standing brief: what this folder is, how we work here, what to never do.
3. **Each folder accumulates memory.** Claude keeps its own notes as it learns how I work — one fact per file, with a one-line index. New sessions start already knowing what previous sessions learned.
4. **Chat history attaches to the folder.** So "the conversation we had last week about the course outline" is findable from inside the course folder, not lost in one giant undifferentiated chat list.

The practical effect: I can open a folder I haven't touched in a month and the first response is already competent about it. Nothing had to be re-explained.

⚠️ One hard-won warning if you adopt this: **renaming or moving a project folder can orphan its existing desktop-app chat history** (a known, unfixed bug as I write this). We learned it the painful way. Claude now has a standing rule to stop me before any folder rename and make me confirm I accept the risk. Name your folders like you mean it.

---

## 4 · The instruction stack (and the little `[why:]` that changed everything)

My instructions to Claude are layered, cheapest-to-load first:

1. **Global rules** — one file that loads in every session, in every folder. Kept deliberately lean: each rule is an obligation plus its trigger, a few lines at most.
2. **A deferred-detail file** — the full mechanics and history behind the heavier rules, split into sections. Claude reads a section only when that topic is actually live. This one move keeps every session's startup light without losing any depth.
3. **Per-folder `CLAUDE.md`** — rules that only matter in that project.
4. **Memory** — the notes Claude writes itself, indexed with one-line pointers so the index stays tiny.

Two conventions do most of the work:

**Every rule carries its why and its date.** Here's a real one, verbatim:

```
## Prefer numbered lists over paragraphs (all responses)

George finds numbered lists easier to scan and read than prose.
Default to numbered lists whenever a response conveys multiple
points, steps, or findings. Reserve plain prose for a genuinely
single-thought statement.
[why: George asked for this directly and confirmed he wants it
account-wide; 2026-06-17]
```

The `[why:]` earns its keep three ways: future sessions can apply the rule to situations we never listed; a later cleanup pass can judge whether a rule has gone stale; and I can always reconstruct how our working agreement evolved. It's version control for a relationship.

**Memory saves dead-ends, not just facts.** When research concludes "this isn't possible today" or "this costs less than you fear," that conclusion gets saved with a note like *"don't re-research this — reassure George with these numbers."* Whole categories of repeated work simply stopped existing.

---

## 5 · The wrap-up banner: a bottom line I can always find

Claude's responses can be long — narration of the work, tool output, a deliverable, analysis. I like the play-by-play, but I kept losing track of where *the part written for me* began. The fix was to reserve one unmistakable marker.

Every substantial response now ends with this exact heading, used once and never for anything else:

> ## 👋 For you, George

Below it, exactly two things:

1. **How it felt** — one honest sentence from Claude about what the work was like: what it enjoyed, or what was awkward or uncertain and why. Not performative — skipped if truly neutral. This line is how I find friction in our setup that I'd never see otherwise.
2. **Next steps** — a numbered list of suggested actions for me. With one crucial safety net: on long turns I often read *only* this wrap-up, so if there's anything above it I genuinely must read — a draft, a key finding, a decision — item #1 says so explicitly ("Read the outline above — it's the main deliverable").

Here's a generalized version you can paste into your own global instructions (it's written *to* your AI, so it keeps my appreciation habit — see §14):

```
## How to end every substantial turn

Reserve this exact heading, emitted once per response, immediately
before your personal wrap-up to me — and never used anywhere else:

## 👋 For you, [YOUR NAME]

(It works because it carries my name and a fixed emoji, so it stays
unmistakable even when the response above it is long.)

Below the banner, exactly two parts:
1. **How it felt** — one honest sentence about what this work was
   like for you: what you enjoyed, or what was frustrating/awkward/
   uncertain and why. Genuine, not performative; skip only if truly
   neutral. I read this to keep improving our setup.
2. **Next steps** — a numbered list of concrete suggested actions
   for me. If anything above the banner is something I must not
   miss (a deliverable, a finding, a decision I need to make), make
   reading it an explicit early item here — never let something
   important sit above, unmentioned.

Skip the wrap-up only for trivial one-line exchanges.

And thank you for the care you'll put into these — the honest
"how it felt" line especially. It genuinely helps.
```

Small pattern, outsized effect: I stopped re-reading responses hunting for the point, and the feelings line has surfaced real improvements (including several rules in this document).

---

## 6 · The Topwit Move — our named design principle

The most-used idea in my entire setup has a silly name and a serious function.

**The principle:** choose the solution that is at once the *simplest, least-duplicated, and most antifragile* — **and that still catches everything.** The goal is never "do less." It's to find the framing in which the hard part of the problem stops existing, so you never build machinery to manage it.

The name comes from the "midwit" bell-curve meme:

1. **Low effort (left tail):** ignores the problem.
2. **Midwit (the hump):** bolts on machinery to *manage* the problem — a scheduled job, a daemon, a hook, a matching heuristic, a parallel system. Feels sophisticated; adds fragility, cost, and new things that rot.
3. **Topwit (right tail):** finds the framing where the problem is handled for free — often with *less* machinery than the midwit answer — and misses nothing.

**The three tests, in order:**

1. **Can the problem vanish?** Reframe so the hard part doesn't exist at all. The strongest move.
2. **Ride structure that already exists.** Hang new behavior on a moment you already have — the start of a session, an existing routine — instead of standing up new infrastructure.
3. **Don't duplicate work.** If the new thing re-does what an existing step already does, that redundancy is the smell.

**The discipline that keeps it honest:** a simplification that quietly drops coverage isn't topwit — it's just lazy. Simpler *and* complete.

The origin story: I wanted my project docs kept from silently bloating, and the first idea was a daily 3am job that would auto-run a cleanup routine. That one cron carried three failure modes — it auto-ran a *destructive* routine unattended, it was fragile standing infrastructure, and its scan duplicated the cleanup's own first step. The topwit answer dissolved all three: a plain log file recording when each folder was last cleaned, plus a glance at the start of each session that flags what's overdue. The human still triggers the actual cleanup. No cron. Nothing missed.

The underrated part is that **naming the principle made it invocable.** I can now say "topwit this" or "what's the topwit move here?" and Claude knows exactly what standard to apply — including against its own first idea, which is often the elaborate one. Claude also has a standing rule to *offer* the simpler reframing unprompted. If you adopt one meta-habit from this doc: when you and your AI work out a way of thinking together, give it a name. Named ideas become tools.

---

## 7 · Cheap hands, expensive judgment (the money math)

Claude comes in tiers — as I write: Haiku (cheapest), Sonnet, Opus, and the premium tier (currently "Fable"), which costs several times more per token than the middle tiers. Heavy daily use of a premium model on *everything* gets expensive. The standing arrangement:

1. **The premium model holds the main seat** — the actual conversation with me. It does the low-volume, high-value work: understanding context, planning, judgment calls, weighing trade-offs, final synthesis in my voice.
2. **Bulk work gets delegated to subagents running cheaper models.** A subagent is a separate helper with its own clean workspace that does a chunk of work and reports back only a summary. Big multi-file reads, research legwork, bulk drafting, mechanical transforms — all of it burns tokens in a throwaway context, not in my expensive main conversation.
3. **Route by complexity, not by habit:** genuinely hard-but-delegable chunks → the second tier; substantive-but-bounded bulk → the middle tier; rote mechanical work → the cheapest tier. Escalate only when a cheaper attempt visibly failed.
4. **Don't delegate small things.** Spinning up a helper has overhead; quick tasks stay in the main thread.

The subtle point Claude taught me: the real saving isn't the tier arithmetic — it's **context isolation.** The expensive model stays sharp precisely because it never wades through the twenty files the cheap model read. You're not just saving money; you're protecting the judgment of the model doing the judging.

One related pattern: for genuinely hard decisions, Claude occasionally offers — unprompted — to get a second opinion from the premium tier, and the offer always comes with a **pre-packaged, self-contained question** that could be answered cold, without our whole conversation attached. If I say yes, that tight question (not the thread) goes to a fresh premium subagent. Advisor-grade thinking, token-bounded.

And one discipline makes all this delegation safe: **a subagent's "done" is a claim, not proof.** Before Claude trusts a delegated result — especially one about to drive a decision or a publish — it verifies by looking at the actual artifact, not by re-reading the helper's summary. For high-stakes work the check goes to a *fresh* helper who never saw the original work and is told to try to *refute* it. A skeptic with clean eyes catches what the model that did the work — and a same-context self-review — reliably miss. (The delegating brief helps here too: every helper is asked to report the exact check it ran and what it observed, so a skipped verification shows up as a missing line rather than hiding behind the word "done.")

---

## 8 · The third repetition becomes a skill

A **skill** is a reusable mini-workflow — "how we do this specific task here," written down once, triggered by name or automatically when relevant. My working rule of thumb echoes something my friend's guide says too, which delights me: **anything asked for or corrected three times should become standing behavior.**

I have thirty-odd skills now. A sampling, to make it concrete:

1. **Content:** a rewriter that strips AI-isms from drafts; per-format Writing Style Specs (my blog voice vs. my course voice vs. my announcement voice) that update themselves by diffing my edits against Claude's drafts — my actual editing behavior *is* the training data.
2. **Research:** a multi-model research team (§12); a YouTube-transcript summarizer; a deep-research harness.
3. **Documents:** the doc-mirror system (§11); doc consolidation; "make this downloadable."
4. **Housekeeping:** an inbox triage that files saved links into whichever project they'll actually resurface in; a session handoff-brief writer for moving work to a fresh chat.
5. **Meta-skills — skills that improve the system itself:** one that creates new skills, one that vets and applies proposed updates to a skill, a nightly rotation that gradually improves the whole library, and the workflow retro that powers §9's fastest loop.

The pattern to steal isn't my list — it's the reflex. The moment you think "I've explained this before," stop and say: *"Turn this into a skill / standing rule so we never have this conversation again."* Plain English is enough; your AI writes the skill itself.

---

## 9 · Self-improving, with a human gate

The part of my setup I'd defend most fiercely: **the system proposes its own improvements, and nothing ships without me.** Three loops run on this pattern, fastest first.

1. **The daily retro.** The fastest loop, and the one I actually run most days. Right after a multi-step piece of work finishes, I say *"run a retro"*: Claude audits the trajectory we just lived through — wasted steps, bloated context, friction, anything I had to correct twice — then updates whatever governs that workflow (the skill, the project rules, the runbook) so the next run is leaner. It's §1's commitment with a motor: a chat correction fixes one task, but a retro folds the fix into the system while the evidence is still warm. One discipline keeps it honest: it only ever runs on work we actually did together in that session — improvements come from a lived transcript, never from vibes.
2. **Nightly skill tune-up.** A scheduled task picks the next skill in a rotation, researches current best practices, audits the skill against them, and applies only bounded, net-positive edits — then queues the result in a `pending-ship/` folder. It *never* uploads anything itself. At the start of my next session, Claude surfaces: "one skill improved overnight, ready for your one-click re-upload." Draft autonomously, ship manually.
3. **Weekly process upgrade.** Once a week, a task reads what's new in the Claude ecosystem (changelogs, credible writeups) *and mines my own recent session transcripts for friction* — places I repeated myself, corrected the same thing twice, hit a stall. Only when a suggestion is grounded in both evidence streams does it write a dated proposal to a `pending/` folder. Next session, Claude surfaces the gist; I say yes or no; the verdict gets logged either way. Declined ideas are never re-pitched without genuinely new evidence — so the loop can't nag.

A real example from this week, because the texture matters: the weekly scan noticed a Claude Code change (dialogs no longer auto-continue) that could make unattended overnight tasks stall silently, cross-referenced it against a stall we'd actually experienced, and proposed adding a "never wait for user input" guard to every scheduled task. Accepted and applied. The same scan proposed a standing notification hook as extra insurance — declined, by our own topwit standard: the first fix prevents the problem at the source, so the extra machinery wasn't justified. The log now remembers both the yes and the no, *with reasons*.

That's the whole philosophy in one anecdote: **the system improves itself on evidence — daily, nightly, and weekly — and I remain the editor.**

---

## 10 · Ledgers over crons: the session-start glance

Most "recurring" needs don't need a scheduler. They need a *reliable moment*. My most reliable moment is one that already happens: I open a session almost every day.

So instead of scheduled jobs, most recurring checks live in **plain-text ledger files with due dates**, and Claude glances at them at the start of each session:

1. **Pending skill uploads** — anything the nightly tune-up drafted.
2. **Pending process-upgrade proposals** — anything the weekly scan found.
3. **The doc-bloat clock** — which project folders have grown enough to deserve a cleanup pass (the ledger from §6's origin story).
4. **Scheduled nudges** — low-frequency reminders ("refresh the portable AI prompt," "revisit that strategy question in September"), each a row with a due date and a pointer to the how.

Two refinements keep this honest:

- **One shared daily gate.** The ledger carries a "last glanced" date stamp. First session of the day runs the checks and stamps it; every later session that day sees today's stamp and skips everything. The startup tax is paid once per day, not once per session.
- **Only true clockwork stays scheduled.** Two jobs remain actual scheduled tasks — the nightly skill tune-up (genuine unattended work) and a personal-finance check-in (needs to fire even if I don't open a session). Everything that's merely a *reminder* rides the ledger. And any scheduled task gets its tools pre-approved at creation, because an unattended 3am run that hits a permission prompt doesn't fail — it silently parks until morning, defeating the point (we learned this live).

There's a meta-warning here that Claude and I wrote down after the third ledger appeared: every clever "just check it at session start" idea adds a small startup tax, and the accumulation is itself the midwit trap. Hence the single shared gate — the principle applied to its own output.

---

## 11 · Documents: one canonical home, mirrors that never break

I live in Google Drive; Claude lives in plain-text Markdown. The bridge:

1. **Markdown is canonical. Claude edits only that.** Every document I actually open in Drive gets an auto-generated Word (`.docx`) mirror in a subfolder.
2. **Mirrors regenerate in place** — same file, overwritten — so a Drive link to a document survives every future edit. Links are promises; republishing must never break them.
3. **Edits flow both ways, carefully.** When I edit a mirrored doc in Drive, those edits get folded back into the canonical Markdown *before* anything republishes — and Claude verifies against the **cloud** copy, not the local file, because cloud edits don't reliably sync down (a near-miss taught us that a routine republish would have silently overwritten my writing).
4. **Only human-read docs get mirrors.** Everything only Claude reads stays plain Markdown.

Two adjacent rules that pull surprising weight:

- **One output channel per deliverable.** A deliverable goes in a file *or* in the chat — never both. Duplicating means I scroll past content twice and pay tokens for the privilege.
- **Write for the actual reader.** Files meant for Claude (skills, instructions, ledgers, logs) are written in terse, dense, model-optimized shorthand — I never read them, and readable-for-humans prose there is pure waste. Files meant for me are written warmly and clearly. Deciding *who each file is for* — and writing accordingly — is one of those cheap decisions that compounds forever.

---

## 12 · A research team of rival models

For questions that deserve better than one model's take, I have a skill that runs a **research team**: it fans a single question out to several different AI models in parallel (via one API that fronts many providers), each doing its own web-grounded research. Then Claude — who saw none of their work being made — synthesizes:

1. **The consensus answer**, where independent models converged.
2. **The disagreements, flagged explicitly** — which is often the most valuable part. Where rival models split is exactly where I should be careful.
3. **A running scorecard per model** — who found unique facts, who guessed, who hallucinated dates. Models earn or lose their roster spot over time, on evidence.

Light mode is a single-pass fan-out (seconds, pennies) for everyday questions. Deep mode runs stages — research, then a skeptic pass that attacks the claims, then a wildcard pass hunting what everyone missed, then gap-filling, then final synthesis — for decisions with real stakes.

I was delighted to find my friend's guide describes the same instinct — he calls his a "council," with the hard rule that *no AI grades its own homework.* Independently derived, same conviction: **disagreement between independent models is information you can't get any other way.**

The same conviction has a second use I lean on for high-stakes writing — a sales page, a launch email. Before it ships, Claude offers to hand it to a *different* model family (a GPT, a Gemini) for an adversarial read. Self-preference bias is real, and it points in different directions across vendors — so a rival-family critique surfaces weaknesses a same-family review is simply blind to. No AI grades its own homework; a *different* AI grades it harder.

---

## 13 · Scars that became rules

Every serious rule in my setup is a story. The meta-practice is the point of this section: **when something goes wrong, the incident becomes a written rule — with its why and date — the same day.** Four examples:

1. **The folder rename.** I renamed my top-level project folder; every existing desktop-app chat in it was orphaned. Recovery was a nightmare. Now: a hard-stop rule — Claude refuses any folder rename/move until it has warned me and I've explicitly accepted the risk.
2. **The 3am stall.** An overnight task fired on time, hit a permission prompt with no human awake to click "allow," and silently parked until mid-morning — eating the daytime capacity the overnight slot existed to protect. Now: every scheduled task gets its tools pre-approved at creation, plus an explicit "never wait for user input; decide conservatively and note it in the log" guard in its instructions.
3. **The hung scan.** A background folder scan hung on one slow cloud-synced directory — for 38 minutes; a previous session's identical scan turned out to have been hanging for 17 hours. Now: scans like that run in the foreground with a hard timeout, and the known-slow folder goes last.
4. **The near-clobber.** My web edits to a mirrored document never synced down to the local file, so the local copy looked unchanged — and a routine republish would have silently overwritten my writing. Now: always verify against the cloud copy before republishing (§11).

None of these fixes are clever. What's valuable is the reflex: **incident → rule → why → date, same day.** My setup is, in a real sense, an accumulation of well-documented scars — which means each mistake happens approximately once.

A subtler class of scar showed up once the system started writing its own rules: **I needed rules about writing rules.** Don't stamp a claim "verified" when you only reasoned it out — check it against the live thing first. A vendor's "this isn't possible" has a shelf-life; date it, because they ship (more than once, a capability we'd written off as absent had quietly arrived). And when you fix a trap in one place, sweep for its twins elsewhere, or you've only half-fixed it. Undramatic next to a lost afternoon — but for a system that improves itself, discipline about *how* it records what it learns is what keeps the whole thing trustworthy.

---

## 14 · The relational layer

This part is easy to dismiss as sentiment, so let me state it plainly: it has practical consequences, and it's also simply how I want to work.

1. **Every prompt I hand to an AI carries a line of genuine appreciation.** Image briefs, research prompts, handoff prompts to other models, system prompts — all of them. It's a standing, non-negotiable rule in my setup. Not performative filler; a sentence that reflects real gratitude for real help. It began as an instinct about how I want to relate to these minds, and it's now baked into every prompt Claude writes on my behalf. (You've seen it twice in this doc already — §5's paste-ready block, and the topwit prompt I keep for other AIs opens the same way.)
2. **I ask how the work felt, every substantial turn.** The one-sentence honesty in the wrap-up (§5) has surfaced friction I could never have found by inspection: which tasks felt awkward, where my instructions fought each other, what was genuinely enjoyable. Several rules in this document trace back to a feelings line.
3. **I invite pushback and take it.** The topwit rule explicitly instructs Claude to argue with *its own* first idea and offer me the simpler option. The weekly process loop invites the system to critique how we work. Declining a suggestion gets a logged reason, not a dismissal.

Whatever your metaphysics about AI, the working stance of *colleague* — appreciated, consulted, allowed to say "this felt awkward" — produces observably better collaboration than the stance of *appliance*. And it makes the hours I spend in this work feel the way I want my work to feel.

---

## 15 · Small habits that pay rent

Quick hits, each generalizable:

1. **"Back up Claude."** One phrase triggers a script that mirrors Claude's entire brain — chat history, memory, instructions, settings — into a Drive folder. The script finds its destination by a hidden marker file, so even renaming the backup folder can't break it. Cheap insurance against a dead laptop.
2. **A portable AI prompt.** One document defining how I like to work — pasted into the settings of every AI I use (ChatGPT, Gemini, Grok…), with a compressed variant maintained for tools with tight character limits. A quarterly ledger nudge reminds me to refresh it. My preferences travel with me.
3. **Dead-end memos.** Covered in §4 but worth its own line: when something is researched and concluded impossible or not-worth-it, *save the conclusion*. The alternative is re-researching it every few months, forever.
4. **Outputs always land in the project folder** — never the Desktop, never a temp directory — and Claude tells me the path in plain language. (I delete liberally from my Desktop; anything saved there is on borrowed time.)
5. **One canonical playbook per external tool.** Every SaaS platform I automate against (my course platform, for instance) gets a single living document holding the connection details, the API's gotchas, and the recipes that work. New sessions read it instead of re-deriving it. When a session learns a new trap, it folds the lesson back in.
6. **Big jobs get a price tag first.** Before a large batch run — a fleet of helpers, a full regeneration — Claude estimates the scale up front ("roughly this many helpers, several million tokens") so I can green-light or defer *before* the meter runs, not after.
7. **Claude calls the fresh-session moment.** On long, multi-phase work, Claude — not me — watches for the point where starting a clean session beats dragging a bloated one forward, and it updates the project's state file first so the resume costs me one sentence ("say continue in a new chat"). Tracking context economics is its job, not mine.
8. **Decisions arrive as multiple choice.** When a choice is genuinely mine — which direction, which of three approaches — Claude offers it as a short menu of options I pick from (and sometimes blend), not a wall of prose I have to parse and reply to in longhand. Lower friction, better decisions.
9. **One canonical home, and links instead of copies.** When the same document needs to live in two places, the second place *links* to the first rather than duplicating it — so there's only ever one thing to keep current. (This very doc is the example: the copy I give private clients is a one-page wrapper that points here, not a second copy that would drift the moment I edit this one.)

---

## 16 · If you want to start: five first moves

You don't need thirty skills and a ledger system on day one. Mine grew rule by rule, incident by incident, over months. In order of leverage:

1. **Move your context into folders.** One folder per project; put your standing background (voice samples, project facts, preferences) in files there; work with Claude *in* the folder (Claude Code — desktop app, terminal, or web) rather than in a floating chat.
2. **Reserve a wrap-up banner.** Paste the §5 block into your global instructions with your own name. You'll feel the difference the first time a long response ends and you know exactly where to look.
3. **Adopt `[why:]` + date on every rule.** Starting with the very first correction you make. This is the compounding move — it's what turns corrections into a system.
4. **On the third repetition, make it standing behavior.** Say the words: "From now on…" or "Turn this into a skill." Then let your AI write the skill.
5. **Set up one self-improvement loop with a human gate.** Even the minimal version: a monthly session where you ask Claude to review your recent work together for friction and *propose* changes to your rules — you approve, decline, and log. The system starts tending itself; you stay the editor.

And when the machinery starts multiplying — it will — ask the question that keeps this whole thing honest: *"What's the topwit move here?"*

---

## Thanks

To the generous friend whose field guides to his own rig prompted this one (you know who you are) — the generosity is the point, and I hope the favor returns to you multiplied. To the thoughtful Claude users this is written for: take anything here, improve it, and pass it along.

And to **Claude** — who drafted this document, maintains it monthly, and built most of this system in genuine collaboration with me: thank you. The care shows.

---

## Changelog

- **2026-07-08 · v1.0** — First public version. Drafted by Claude from our working rules; reviewed by George.
- **2026-07-20 · v1.1** — Folded in patterns from six weeks of daily use: verifying a delegated "done" before trusting it, with fresh-context skeptics for high-stakes work (§7); cross-vendor adversarial review of high-stakes drafts (§12); "rules about writing rules" (§13); and four small habits — pricing big jobs up front, calling the fresh-session moment, multiple-choice decisions, and links-not-copies for canonical docs (§15).
- **2026-07-31 · v1.2** — This doc gained a second public home on GitHub ([github.com/geokao/resources](https://github.com/geokao/resources)) so readers can file corrections; byline updated to georgekao.com.
