# Tempo: a smart planner that works around your real week

Tempo turns a messy to-do list into a realistic plan for today. It knows your classes, events and time blocks, scores every task by priority and deadline, and fits focus time into the gaps. You can even snap a photo of a homework list and Tempo turns it into tasks.

Built for the Human + AI Hackathon.

## Features

**Dashboard**: active task timer, productivity summary (today, this week, completed, in progress), deadline alerts, upcoming deadlines, today's schedule, browser alerts, one-click overdue rescue.

**Tasks**: search, category and priority filters, tabs (All / Pending / In progress / Completed), bulk Select (mark done or delete), expandable notes, Start timer, Log time, logged vs estimated time, weekly repeat.

**Schedule**: Classes, Events (one-off dates), Time blocks (recurring busy time), and Availability (the hours Tempo may schedule per day). All with colors, locations, edit and delete.

**Analytics**: total focus, this week, completion rate, estimation accuracy, deadline performance (due today, overdue list, on-time rate), 7-day chart, time-by-category donut.

**Extras**
- **Workload forecast**: a 7-day chart of work left versus free time, with overload warnings before a crunch hits.
- **Self-calibrating estimates**: Tempo learns how long your tasks really take and plans with your real pace.
- **Break it down**: Claude splits a big task into small steps you review and add.
- **Voice quick add**: tap Speak and say a task.
- **XP, levels and streaks**, with confetti when you finish a task.
- **Export to calendar**: download today's plan as an .ics file for Google, Apple or Outlook.

**New in Tempo**
- **Plan my day**: scheduler fits tasks around classes, events and blocks, with breaks.
- **Quick add in plain English**: `Math worksheet tomorrow 5pm high 45m #School weekly`.
- **Add from photo**: upload a picture of a handwritten or printed task list. Claude reads it, you review and edit the tasks, then add them.
- **Ask Claude**: a short coaching note on today's plan.

## Human + AI collaboration

| Human | AI |
|---|---|
| Sets goals, classes, availability, final plan | Reads photos and turns them into structured tasks |
| Reviews and edits every imported task before it is saved | Parses natural language, reviews the plan for risks |
| Logs real time, which sharpens estimates | Suggests, never commands |

The scheduler is a transparent algorithm (below), so you can see why a task landed where it did.

## Run it

No build step, no dependencies.

```bash
cd juniper-planner
python3 -m http.server 8000   # visit http://localhost:8000, or just open index.html
```

Data is stored in your browser's `localStorage`.

### Turn on the AI features

1. Click **AI settings** and paste an Anthropic API key (console.anthropic.com). Default model: `claude-sonnet-5-5`.
2. **Add from photo** on Dashboard or Tasks, or **Ask Claude** on Dashboard.

The key stays in your browser and is sent only to `api.anthropic.com` (direct browser access). Use a key with a low spend limit and never deploy this publicly with a shared key. For production, add a small server in between. Photos are resized in the browser and sent only to the API.

## How the scheduler works

```
score   = priority_weight * 10 + urgency
urgency = 40 if overdue else max(0, 30 - hours_until_due / 2)
```

Starting from now (rounded to 15 min, inside that day's availability), Tempo skips classes, events and blocks and fills each gap with up-to-50-minute chunks of the highest-scoring task, minus time already logged, with 10-minute breaks. Tasks that don't fit roll to tomorrow.

## Limitations and next steps

- Data lives in one browser. Next: sync and export.
- Plans only today. Next: multi-day planning toward big deadlines.
- Browser alerts only fire while the tab is open.
- Photo import works best with clear, well-lit, straight-on photos.
- Next: let Claude propose a reordered plan to accept in one click; ICS calendar import.
