# job-apply — a Claude Code skill

A reusable [Claude Code](https://claude.com/claude-code) **skill** for running a job-application
campaign via Chrome DevTools MCP browser automation. It scans company career boards, fills ATS
forms (Greenhouse / Ashby / Lever / Workday), drafts application emails, and tracks everything in a
CSV — answering every form field from a single profile file.

Battle-tested across 100+ real applications.

## What's here

| File | What it is |
|---|---|
| `job-apply/SKILL.md` | The **engine** — generic playbook. Board-scanning, per-ATS form recipes (with the exact JS setters that reliably commit React-controlled inputs), known blockers, honesty rules. Person-agnostic. |
| `job-apply/candidate.template.md` | A **placeholder profile**. Copy to `candidate.md`, fill in the `{{TOKENS}}`, and the engine reads your data from it. |
| `.gitignore` | Ignores `candidate.md` so your personal data never gets committed. |

## Design

All person-specific data lives in **one** file (`candidate.md`): identity & contact, résumé paths,
education, profile bullets for essays, **work authorization**, and targeting (location / seniority /
comp). The engine in `SKILL.md` never changes — to run it for a different person, you swap
`candidate.md`. Nothing else.

The skill is built around a few hard rules:

- **Never lie on a screening gate.** Hard year minimums ("5+ years"), citizenship-only, or
  no-sponsorship requirements that don't match the candidate → skip the role, don't fabricate.
- **Work authorization is the most candidate-specific field** — it's mapped fresh from the profile
  on every form, never carried over.
- **Ask, don't guess** on identifying info (contact, legal-name spelling, address).
- **Decline** all EEO / voluntary self-id.
- **Don't spray** the same company; respect reapply limits.

## Setup

1. Install [Claude Code](https://claude.com/claude-code) and the
   [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp) server.
2. Copy this `job-apply/` folder into your skills directory (`~/.claude/skills/job-apply/`).
3. `cp job-apply/candidate.template.md job-apply/candidate.md` and fill in every `{{TOKEN}}`.
4. In Claude Code, ask it to apply to a role or scan a careers page — it loads the skill and works
   from your `candidate.md`.

## ⚠️ Privacy

`candidate.md` contains personal data (contact info, home address, work-authorization / visa status,
comp expectations). It is **git-ignored** in this repo on purpose — keep it that way. Only the
engine and the placeholder template are meant to be public.

## License

MIT
