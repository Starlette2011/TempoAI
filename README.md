# Tempo: a smart planner that works around your week

Tempo turns a messy to-do list into a realistic plan for today. It knows your classes, events and time blocks, scores every task by priority and deadline, and fits focus time into the gaps. You can even snap a photo of a homework list and Tempo turns it into tasks.

## Features

**Dashboard**: active task timer, productivity summary (today, this week, completed, in progress), deadline alerts, upcoming deadlines, today's schedule, browser alerts, one-click overdue rescue.

**Tasks**: search, category and priority filters, tabs (All / Pending / In progress / Completed), bulk Select (mark done or delete), expandable notes, Start timer, Log time, logged vs estimated time, weekly repeat.

**Schedule**: Classes, Events (one-off dates), Time blocks (recurring busy time), and Availability (the hours Tempo may schedule per day). All with colors, locations, edit and delete.

**Analytics**: total focus, this week, completion rate, estimation accuracy, deadline performance (due today, overdue list, on-time rate), 7-day chart, time-by-category donut.

**Extras**
- **Workload forecast**: a 7-day chart of work left versus free time, with overload warnings before a crunch hits.
- **Self-calibrating estimates**: Tempo learns how long your tasks really take and plans with your real pace.
- **Break it down**: splits a big task into small steps you review and add.
- **Voice quick add**: tap Speak and say a task.
- **XP, levels and streaks**, with confetti when you finish a task.
- **Export to calendar**: download today's plan as an .ics file for Google, Apple or Outlook.

**New in Tempo**
- **Plan my day**: scheduler fits tasks around classes, events and blocks, with breaks.
- **Quick add in plain English**: `Math worksheet tomorrow 5pm high 45m #School weekly`.
- **Add from photo**: upload a picture of a handwritten or printed task list. Text is read on your device, turned into tasks, and you review and edit them before they are added.
- **Coach me**: a short coaching note on today's plan.

## Human + AI collaboration

| Human | AI |
|---|---|
| Sets goals, classes, availability, final plan | Reads photos and turns them into draft tasks |
| Reviews and edits every imported task before it is saved | Parses natural language, splits big tasks, flags risks in the plan |
| Logs real time, which sharpens estimates | Suggests, never commands |

The scheduler is a transparent algorithm (below), so you can see why a task landed where it did.

## Quick start: no setup

Open `index.html` in Chrome, Edge or Firefox. Create an account with an email and password. Your planner is encrypted on this device with that password, so other people using the same browser cannot read it, and each email gets its own separate planner.

**Lost passwords cannot be recovered. If you forget your password, your account data is permanently lost.** The data is encrypted with the password, so nobody, including the developer, can get it back. The app says this on the sign-in and sign-up screens. **Clearing your browser data also deletes everything.** Local accounts are saved in the browser, so clearing cookies and site data (often bundled into "delete history"), or using a private window, erases the account and its tasks with no way to restore them. The app warns about this on the sign-in and sign-up screens. If you forget your password, "Forgot password?" lets you erase that account and create a fresh one with the same email.

Local accounts live in one browser on one device. For accounts that sync across devices and real email password resets, use the optional cloud mode below. Tempo switches to cloud mode automatically when Supabase keys are filled in.

## Optional: cloud accounts with email reset

Anyone can create an account with an email and password. Forgot your password? Tap "Forgot password?" and a reset link arrives by email. Tasks sync across devices because they are stored in the cloud.

- **Auth and storage:** Supabase Auth (email + password, confirmation and reset emails) and one Postgres row per user.
- **Row Level Security:** the database itself only lets a signed-in user read or write their own row (see `schema.sql`). Other users cannot see your data, even though the app code is public.
- **No API keys and no AI accounts.** All AI features run on your device or inside the page. Photos never leave your browser.
- **A Content Security Policy** limits the page to loading its own code and the on-device reader from jsDelivr (plus Supabase in cloud mode).
- **Sign out** saves, clears everything from the screen and ends the session. Use it on shared computers.
- Honest limits: data is encrypted in transit and at rest by Supabase, but it is not end-to-end encrypted, because email password recovery and end-to-end encryption cannot both work. Whoever owns the Supabase project can technically access the database. Two devices editing at once: the last save wins.

### Cloud setup (10 minutes)

1. Create a free project at supabase.com.
2. SQL Editor: paste and run `schema.sql`.
3. Project Settings, API: copy the **Project URL** and the **anon public key** into `SUPABASE_URL` and `SUPABASE_ANON_KEY` near the top of the script in `index.html`.
4. Authentication, URL Configuration: set **Site URL** to where the app runs (for example `http://localhost:8000/` while testing) and add the same address, plus your deployed address, under **Redirect URLs**. Reset and confirmation links only work for listed addresses.
5. **Email for everyone:** Supabase's built-in email sender is limited to a few emails per hour, which is fine for testing but not for the world. Under Authentication, SMTP Settings, plug in a provider such as Resend, Brevo or Postmark. You can also edit the email templates there.
6. Optional: Authentication, Providers, Email: set your minimum password length (the app asks for 8) and decide whether to require email confirmation.

## Run it

```bash
cd juniper-planner
python3 -m http.server 8000   # open http://localhost:8000
```

Local mode works when you open `index.html` directly (if your browser blocks encryption on files, use the command above). Cloud mode needs a real web address, because email links cannot point at a file. To let anyone use it, upload the folder to Netlify, Vercel or GitHub Pages and add that address to the Redirect URLs.

### The built-in AI (no key needed)

Nothing to configure. Everything works the moment you open the app:

- **Add from photo** uses Tesseract.js, an on-device text reader. The photo stays in your browser. The first use downloads the reader (a few MB) from jsDelivr, so it needs an internet connection once. Printed text and neat handwriting work best; messy handwriting may need fixing in the review step. Lines are turned into tasks with the same plain-English parser as quick add.
- **Coach me** and **Break it down** use your browser's own on-device model when it has one (for example Chrome's built-in Prompt API) and fall back to Tempo's built-in logic everywhere else. The built-in coach ranks your tasks, checks overdue work and overload days, and the built-in breakdown picks steps that fit the kind of task (study, writing, project, homework).
- You review and approve everything before it becomes a task.

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
- Next: a smarter on-device model for photos and coaching; ICS calendar import.
