# Tempo: a smart planner that works around your real week

Tempo is a student planner that turns a messy to-do list into a realistic plan for today. It knows your classes and activities, scores every task by priority and deadline, and fits focus blocks into the gaps between them. An optional AI coach (Claude) reviews the plan and points out risks.

Built for the Human + AI Hackathon.

## The problem

Planners usually show you a list and leave the hard part to you: deciding what to do *right now*, given that you have taekwondo at 4:45 and a test on Friday. Overdue tasks pile up, estimates are wrong, and the list stops being trustworthy.

## What it does

- **Plan my day**: an offline scheduler scores each task (priority + deadline urgency, overdue first), then places 50-minute focus blocks with short breaks into the free time around your classes.
- **Quick add in plain English**: type `Calc worksheet tomorrow 5pm high 45m #School weekly` and Tempo extracts the due date, time, priority, length, category and repeat.
- **Ask Claude (optional)**: sends your tasks and plan to Claude, which replies with what to start now, one risk, and one tweak.
- **Classes**: recurring commitments the planner never double-books.
- **Focus timer**: start a task, stop to log the time. Tempo compares your estimates with reality.
- **Insights**: focus time, completion rate, on-time rate, estimate accuracy, a 7-day chart and a category breakdown.
- **Overdue rescue**: one click moves every overdue task to tomorrow night so the planner can schedule it.
- Light and dark mode, keyboard-friendly, responsive on phones.

## Human + AI collaboration

| The human | The AI |
|---|---|
| Owns the goals, deadlines, classes and final plan | Parses natural language into structured tasks |
| Can edit or ignore any suggestion | Reviews the plan and flags risks in under 120 words |
| Logs real time, which corrects the estimates | Suggests, never commands |

The scheduler itself is deliberately a transparent algorithm, not a black box, so you can see why a task landed where it did. AI is used where language and judgment help most.

## Run it

No build step and no dependencies.

```bash
cd juniper-planner
# option 1: just open index.html in a browser
# option 2: serve it locally
python3 -m http.server 8000   # then visit http://localhost:8000
```

Data is saved in your browser's `localStorage`. Nothing leaves your device.

### Turn on the AI coach

1. Click **AI settings** in the sidebar.
2. Paste an Anthropic API key (from console.anthropic.com). The default model is `claude-sonnet-5-5`; change it if you like.
3. On the Today screen, click **Ask Claude**.

The key is stored only in your browser and sent only to `api.anthropic.com`. Because the call is made straight from the browser, use a key with a low spend limit and don't deploy this publicly with a shared key. For a real deployment, put a small server in between.

## How the scheduler works

```
score = priority_weight * 10 + urgency
urgency = 40 if overdue else max(0, 30 - hours_until_due / 2)
```

Tasks are sorted by score. Starting from now (rounded to 15 minutes, no earlier than 8 AM), Tempo walks through the day, skips class times, and fills each free gap with chunks of up to 50 minutes (minus time you've already logged). A 10-minute break follows each block. Anything that doesn't fit is reported as rolling to tomorrow.

## Project layout

```
index.html   the whole app: HTML, CSS, JavaScript
README.md    this file
```

Fonts: Bricolage Grotesque (headings) and Figtree (body) from Google Fonts, with system fallbacks if offline.

## Limitations and next steps

- Data lives in one browser. Next: sync and export/import.
- The planner only plans today. Next: multi-day planning toward big deadlines.
- Ask Claude is advice only. Next: let Claude propose a reordered plan the user can accept with one click.
- Quick add understands common phrases, not everything. Next: send unclear input to Claude to parse.
- Calendar import (ICS) for classes.
