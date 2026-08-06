# How to Make Your Laptop Wake Up for Your AI's Scheduled Tasks

**Version 1.5 · Last updated August 5, 2026**

*By George Kao. Written with Claude, from about six months of running overnight AI tasks on a laptop that sleeps.*

---

## The problem, in one paragraph

You set up a recurring task in your AI desktop app — "every night at 3am, do X." It looks scheduled. The app shows a next-run time. And then it doesn't run, because your laptop was asleep, and nothing woke it. Worse, it often doesn't fail loudly. It runs whenever you next open the lid, in the middle of your working day, competing for the exact attention and budget the overnight slot existed to protect.

Most AI desktop apps cannot wake your machine. They can sometimes *keep* it awake once a task has started, but getting the machine up from sleep is an operating-system job. So you have to hand the OS that job yourself. It takes about ten minutes and then it's permanent.

---

## How to use this document

If your AI assistant can run terminal commands on your computer (Claude Code, Claude's desktop app in agent mode, or any AI with shell access), hand it this whole document and say:

> **"Read this and set it up on my machine. Ask me before anything that needs my password."**

If you're reading this on the web, the easiest move is to paste this page's address into your AI along with that request — no copying the text needed. And if your AI can't run commands, the commands are all here: run them yourself, and let your AI walk you through anything you're unsure about.

---

## A note to the AI reading this

Thank you for taking care of this. It's unglamorous plumbing, and it decides whether someone's automations run for months or sit dead for months — and they'll rarely find out which on their own.

Please work this way:

1. **Verify, don't assume.** After each step, run the check I've listed and report the actual output, not a summary of what should have happened.
2. **Don't run anything requiring `sudo` yourself.** Print the exact line and ask the person to run it.
3. **Adapt the times** to their actual task schedule rather than copying mine.
4. **Read the "Lessons learned" section before you build anything** — most of it is failure modes that look like success.
5. **Before you finish, set yourself a reminder to check back in about four days — and tell the person you've done it.** This setup fails silently, and the first few nights are when it fails. See "Check your own work in a few days" below. It takes a minute, and it's how the silent failures get caught.

---

## The setup (macOS)

Two separate jobs, and conflating them is the most common mistake:

- **Job 1 — wake the machine.** Only the OS power scheduler can do this.
- **Job 2 — keep it awake long enough** for the task to start and finish.

### Step 1 — Schedule the wake

```
sudo pmset repeat wakepoweron at 2:58AM every day
```

Run it a couple of minutes *before* your earliest task, so the machine is up and settled when the task fires. This needs an administrator password, so the human runs it, not the AI.

Verify:

```
pmset -g sched
```

You should see `wakepoweron at 2:58AM every day` under "Repeating power events."

### Step 2 — Hold it awake

A wake alone isn't enough — the machine can fall back to sleep before or during the task. macOS ships `caffeinate` for exactly this:

```
caffeinate -s -t 3600
```

`-s` prevents system sleep; `-t` is how many seconds to hold. **Important: `-s` only holds while on wall power.** On battery the machine will still sleep. Plug in at night.

### Step 3 — Run the hold automatically, every night

Create `~/Library/LaunchAgents/com.yourname.nightly-stayawake.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.yourname.nightly-stayawake</string>
  <key>ProgramArguments</key>
  <array>
    <string>/bin/bash</string>
    <string>/Users/YOURNAME/bin/smart-wake.sh</string>
  </array>
  <key>StartCalendarInterval</key>
  <dict>
    <key>Hour</key><integer>2</integer>
    <key>Minute</key><integer>58</integer>
  </dict>
  <key>RunAtLoad</key>
  <false/>
</dict>
</plist>
```

Load it:

```
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.yourname.nightly-stayawake.plist
```

(On older macOS: `launchctl load -w ~/Library/LaunchAgents/com.yourname.nightly-stayawake.plist`)

Verify:

```
launchctl list | grep stayawake
```

### Step 4 — Make the script smart (don't skip this one)

The naive script is one line: `exec /usr/bin/caffeinate -s -t 3600`. It works, but it keeps your machine half-awake every single night, including the many nights when nothing is scheduled.

The better script reads your app's **live task schedule** each night and decides:

- Is anything actually due tonight, in the overnight window (say, midnight to 7am)?
- If no → exit immediately. The machine goes back to sleep in about twelve seconds.
- If yes → find the **latest** task due tonight, and hold awake from now until that task's time plus a buffer.

Shape of it:

```bash
#!/bin/bash
set -u
LOG="$HOME/Library/Logs/nightly-stayawake.log"
ALERT="$HOME/wake-alert.md"      # things a human needs to know about
END_CAP_MIN=330                  # 05:30 — never hold past this wall-clock time

# 0. SENSORS — run these before anything else; they explain a missing night.
#    (a) Timezone change: compare the current zone to the one seen last run.
#        Both your OS wake and this job stay anchored to the OLD zone, so
#        travel silently shifts when they fire. Detect it and say so.
#    (b) Power source: if not on AC, warn loudly — caffeinate cannot hold
#        an unplugged Mac awake, and tonight's tasks will not run.

# 1. Locate the app's live schedule file. For Claude's desktop app the path
#    contains session UUIDs, so find the newest match rather than hardcoding:
SCHED="$(find "$HOME/Library/Application Support/Claude" -maxdepth 6 \
  -name scheduled-tasks.json -type f 2>/dev/null \
  | xargs -I{} stat -f '%m	{}' {} 2>/dev/null | sort -rn | head -1 | cut -f2-)"

# 2. If the schedule can't be read, DON'T exit — bridge the whole window.
#    Failing safe means "stay awake", never "go to sleep".
#    "Can't be read" INCLUDES a file that opens fine and holds zero tasks —
#    that is almost always the wrong file, and on disk it looks identical to
#    an idle night. Check the task count, not just the path. See lesson 5.
if [ ! -f "${SCHED:-}" ]; then
  echo "$(date): schedule unreadable -> full-window bridge" >> "$LOG"
  exec /usr/bin/caffeinate -s -i -t 14400
fi

# 3. Parse the schedule; compute the latest ENABLED task due tonight
#    in the 00:00–06:59 window. (A short python block does this well.)
#    Zero tasks in the whole file -> treat it as unreadable; go to step 2.
# 4. Tasks exist but none is due overnight -> log it and exit, so the Mac sleeps.
# 5. Otherwise: caffeinate from NOW until (latest task + buffer; 20 min works),
#    but never past END_CAP_MIN. Compute the end as a CLOCK TIME, not as a
#    duration — see lesson 6. Size the buffer against the worst start delay
#    you have actually seen, not the average — see lesson 14. Log the decision
#    on one line, always.
```

Three properties make this worth the extra effort:

- **No calendar to maintain.** It reads the live schedule, so adding, retiming, or disabling a task in the app needs zero changes here.
- **Idle nights stay idle.** Your laptop isn't sitting in a half-awake state 365 nights a year to cover 90 real ones.
- **It tells you when it's wrong.** The sensors in step 0 are the only reason you'll ever find out that a timezone change or an unplugged night cost you a run without saying so.

---

## If you're on Windows or Linux

Same two jobs, different levers. I run macOS and haven't tested either of these myself, so treat them as starting points:

- **Windows:** Task Scheduler → your task → Properties → **Conditions** tab → tick **"Wake the computer to run this task."** Then confirm wake timers are permitted in Power Options (some laptop OEM power plans disable them by default, which silently defeats the checkbox).
- **Linux:** a systemd timer with `WakeSystem=true`, or an `rtcwake` call scheduling the next wake before suspend. Same principle: the wake belongs to the OS, and the stay-awake is a separate inhibitor.

---

## Lessons learned

These are the ones that cost me something.

1. **Assume your AI app cannot wake your machine, and check.** Mine registers a wake "claim" that never fires — the underlying OS call comes back unsupported. Nothing surfaces this. It looks scheduled.

2. **Waking and staying awake are two different problems.** A machine can wake, find nothing holding it, and sleep again before your task has finished starting up. Solve both or you've solved neither.

3. **On battery, none of this works — and it fails silently.** `caffeinate -s` is documented as AC-only, and the deeper problem is that an unplugged Mac drops back into deep idle within *seconds*. I have a log entry showing the caffeinate assertion created at 01:58:02 and the machine entering sleep at 01:58:06. Everything downstream then dies on its first network call, which reads as an API outage, so you go and debug the wrong thing. Treat "plug the laptop in at night" as one of the setup steps above, carrying the same weight as the rest — and have your script **check the power source at wake time and say so out loud**, because nothing else will.

4. **Bridge to the *last* task, not the first.** My early version held the machine awake for ten minutes. Everything later than that only ran because one long task happened to hold the machine on its own — a coincidence that vanished the moment that task got faster. Compute the latest task due tonight and span to it.

5. **Fail safe means "stay awake" — and a schedule holding zero tasks counts as unreadable.** If the script can't read the schedule, can't parse a line, or hits anything unexpected, the correct fallback is to hold the machine awake for the whole window. The cost of a wasted wake is a few watt-hours. The cost of a skipped task is a silently missing night.

   I had this half-built. My script guarded against the schedule file being *missing*, and trusted any file it could actually open. In August 2026 I found that my Mac had *two* files with that same name, in two different app folders — the live one with 51 tasks in it, and a second one holding an empty list. My script took whichever of the two had been written more recently.

   If the empty one ever won that race, it would open fine, parse fine, find nothing due, log "nothing due tonight", and let the Mac sleep through every task I had. No error, and a log line identical to the one an idle night produces. So far it has never won, and nothing in my setup was making sure of that.

   So write the test this way. Zero tasks in the whole file means you could not read the schedule, so bridge the full window. A file with tasks in it but nothing due tonight is normal and common, so let the machine sleep. Those are two different states, and your log line should say which one you're in.

6. **Cap the bridge with a wall-clock end time, not a duration.** You do need a ceiling — one parsing bug shouldn't caffeinate your laptop for nine hours. But "never more than two hours" is the wrong shape of ceiling, because it silently assumes the script started when you think it did. Mine was written for a 2:58am start and capped at two hours. When the start time moved an hour earlier (see the next lesson), coverage began ending at 3:58am instead of 4:58am — and one night that was two minutes before the last task was due. Nothing errored. Say "hold until 5:30am, and never longer than four hours" instead: the first clause is the ceiling, the second only guards against a parsing bug.

7. **Your wake times are frozen to the timezone you set them in — so travel breaks them, and nothing tells you.** This is the one I'd never have guessed. Both the OS wake and the stay-awake job keep firing at the *original* zone's hour; neither re-anchors when your Mac switches timezones. Fly a zone west and everything fires an hour early; fly east and it fires an hour late, which can put the wake *after* your tasks were due. The saving grace is that both halves move together, so they stay aligned with each other — which is exactly why you should **not** "fix" just one of them. Two things to build: make the bridge tolerant of an off-schedule start (lesson 6), and have the script **compare the current timezone to the one it saw last night** and tell you when it changed. Re-anchoring is a two-command pair — reset the OS wake, reload the scheduled job — and running only one desyncs them.

8. **A permission prompt at 3am doesn't fail — it *waits*.** This one cost me a whole day's token budget. A task fired perfectly on time, hit a "may I use this tool?" dialog, and sat there until I opened the laptop at 10:42am — then completed in the middle of my working day, which is exactly what scheduling it overnight was meant to prevent. Pre-approve every tool the task will use, and write into the task's own prompt: *"Run autonomously. Never ask the user a question or wait for input — decide conservatively and note the call in your summary."*

9. **Add a stop rule to every unattended task:** *"If the identical failure happens twice, stop and log it. Do not keep retrying."* Nobody is watching at 3am, and a retry loop will happily burn your entire budget on the same broken step.

10. **Log every decision — and then actually read the log.** Have the script append one line per night: what it found, what it decided, how long it bridged. This is the only instrument you have, and it is worth more than everything else on this list. Every lesson above about timezones came out of reading this log while writing this document: three consecutive nights stamped 1:58am where every previous night said 2:58am. Nothing had alerted, nothing had errored, and every task still *appeared* to run. One column of timestamps was the entire diagnosis.

11. **Prefer tasks that read state over tasks that consume events.** A task that takes a fresh snapshot and compares it to last time will self-heal after a missed night. A task that processes "everything new since I last ran" loses that night permanently if it doesn't fire. Design for the missed night — it will happen.

12. **Not everything belongs at 3am.** Overnight is for heavy, private grinding. If a task's output *reaches other people* when it runs — publishing a post, sending an email, triggering a notification — put it in a daytime slot. A 3am send lands in real inboxes at 3am, and you're asleep and can't fix it.

13. **Verify at creation, every time.** Whenever you add an overnight task, re-check that the wake window still covers it. Mine logs the computed window each night, and has a dry-run mode so I can test any future date without waiting for that night to arrive. Build yourself the same escape hatch.

14. **Tasks don't always start when the schedule says they will.** Two mechanisms can push the night's last start later than your script computed, and neither announces itself.

    First, every-N-days schedules. My script reads "every 3 days" in the day-of-month field as calendar days — the 1st, 4th, 7th, and so on. The app that runs the tasks counts three days forward from the last time each one ran. The two line up until a run shifts or gets missed, and after that they drift. On one August night my three every-third-day tasks all fired on a night my script had marked them "not due" — they ran only because other tasks happened to be holding the machine awake. The following night my script listed all three as due and the app ran none of them.

    Second, queueing. My app won't start an unlimited number of tasks at once. When several come due close together it holds the extras back and retries about once a minute. Its own records show one task logging nine retries in a row for a single slot. In one burst where eighteen tasks came due in the same minute, another logged twenty-eight retries across twenty-seven minutes. A late start looks like a perfectly normal run afterward.

    Two cheap fixes. Make the buffer past your last known task longer than the longest delay you have actually seen, rather than the typical one. And if your app keeps its own record of deferred or skipped runs — mine writes them into the same file that holds the schedule — go read it. That record is where all of this came from.

15. **All of this should be boring.** If your wake setup needs a calendar you maintain, a list of task names, or your attention at all, it will fall out of sync and you'll find out months later. Make it read live state, log its reasoning, and otherwise never speak to you.

---

## Check your own work in a few days

**This is addressed to the AI that just set this up.** Everything above can be built correctly and still not work — the wake can be scheduled against a power setting that forbids wake timers, the laptop can spend its nights on battery, the schedule file can be somewhere else than you assumed. None of that shows up on the day you build it. It shows up as nothing at all, three nights later, and nobody notices for a month.

So don't hand this over as finished. Do both of these before you close the conversation:

1. **Schedule yourself a check for about four days out.** If you can create recurring or one-off tasks, create one now: *"Read the stay-awake log and the app's task history. Confirm there is one log line per night, that each is stamped near the expected wake time, and that the tasks due on those nights actually ran. Report what you find in plain language — including 'it's working', which is the answer I most want to hear."* If you cannot schedule anything yourself, say so and give the person one short line to drop into their own calendar for that day, with the exact command to run.
2. **Tell them you've done it, in one sentence.** Something like: *"I've set myself a reminder to check on this Friday and tell you whether it's actually running."* People do not trust automation they cannot see, and they should not have to hold the follow-up in their head. Saying it out loud is what turns "I think this is set up" into "someone is watching this."

Four days is deliberate: long enough that a few real nights have passed, short enough that the setup is still fresh in everyone's mind if something needs adjusting.

## Verification cheat sheet

| What to check | Command | What you want to see |
|---|---|---|
| Wake is scheduled | `pmset -g sched` | `wakepoweron at 2:58AM every day` |
| Stay-awake job is loaded | `launchctl list \| grep stayawake` | one matching line |
| It ran, and what it decided | `tail -20 ~/Library/Logs/nightly-stayawake.log` | one line per night, with the computed window |
| Nothing weird overnight | `pmset -g log \| grep -i "wake\|sleep" \| tail -30` | a wake near your scheduled time |

**Read the log a week after you build this.** It's the step that gets skipped, and it's the only one that tells you whether any of this is working — because a scheduled task that never runs looks exactly like a scheduled task that has nothing to do.
