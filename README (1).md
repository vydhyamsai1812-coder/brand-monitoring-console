# Signal — Brand Escalation Console

A single-file, zero-dependency prototype of a brand monitoring & escalation workflow: a live-updating multi-source feed, a signal-based severity/decision engine, and a full human-in-the-loop approve → dismiss → escalate → retract flow — all in one HTML file you can open directly in a browser.

Built as a hackathon project to explore what an AI-assisted "brand defense" triage console could look like, with an emphasis on keeping every consequential action (public replies, takedown flags, account reports) behind explicit human approval.

## Live demo

Open [`index.html`](./index.html) directly in a browser, or serve the repo with any static file server:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

No build step, no npm install, no backend required.

## What it does

- **Live-updating feed** across five simulated sources (Twitter/X, review sites, news outlets, forums) plus a dedicated **Reddit-style lane** you can point at any brand or keyword via the "Track" input
- **Signal-based decision engine** — severity isn't just a fixed label; the console reads the actual detected signals (legal exposure, security claim, privacy allegation, viral trajectory, service complaint, etc.) to decide reply vs. escalate vs. monitor-only, per item
- **Suggested action + draft reply** for every medium/high item, generated from the signal mix
- **Human-approval gate** — low-severity items auto-tag for trend tracking only; everything else waits for a person to approve, dismiss, or escalate. Nothing public ever fires without a click.
- **Scoped removal logic** — "flag for takedown" is only suggested when a post itself exposes private data or a live exploit, never just for being unfavorable criticism
- **Retract / undo** — any action can be pulled back with a logged reason, which reopens the item for a fresh decision
- **Repeat-offender detection** — accounts that rack up multiple severe posts get surfaced for a separate, manual "flag for reporting" decision
- **Live stats dashboard** — open/high counts, resolved, retracted, flagged, reported

## What's real vs. simulated (read this before demoing)

Everything in this console is simulated data — **including the Reddit-style lane.** That's a deliberate design choice, not a shortcut we forgot to fix:

- A browser page rendered inside most sandboxed AI-artifact previews can only call a small allow-list of endpoints — it can't freely `fetch()` arbitrary third-party domains like `reddit.com`. An early version of this project tried a genuinely live `reddit.com/search.json` poll and it failed for exactly that reason.
- Rather than show that error, the Reddit-style lane generates fresh, realistically-worded post text for whatever brand you're tracking and runs it through a real keyword-based `classifyRedditText()` heuristic — so the severity/signal logic is real and testable, even though the input text is synthetic.
- The original live-fetch implementation is kept in the code as `fetchRedditLive()` (unused by default) — Reddit's public `search.json` endpoint generally does allow anonymous CORS `GET` requests from a real browser, so if you self-host this outside a locked-down sandbox, wiring that back in is a small change.
- No action in this console — reply, escalate, remove, report — actually contacts a real platform or person. Every "suggested action" is a recommendation for a human team to execute elsewhere.

## Tech stack

Vanilla HTML, CSS, and JavaScript. No frameworks, no build tooling, no package.json. The whole app is one file so it's trivial to read top-to-bottom, fork, or drop into any static host.

## Project structure

```
.
├── index.html      # the entire application
└── README.md
```

## Roadmap / natural next steps

- Swap the simulated feed for real source integrations (requires paid/approved platform APIs)
- Add a thin backend to proxy an LLM classifier for severity/signals instead of the keyword heuristic, with the API key held server-side
- Persist state (currently everything resets on page reload)
- Multi-brand / multi-tenant tracking in one view
- Audit-log export for compliance review

## License

MIT — do whatever you want with it, just don't claim the simulated data is real.
