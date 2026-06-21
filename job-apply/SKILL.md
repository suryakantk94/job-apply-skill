---
name: job-apply
description: Find and fill/submit software & engineering job applications on a candidate's behalf. Use when applying to jobs, scanning company career boards, filling ATS forms (Greenhouse/Ashby/Lever/Workday), or drafting application emails. Reads the active person from candidate.md.
---

# Job Apply (engine)

Generic playbook for running a job-application campaign via Chrome DevTools MCP browser automation. Battle-tested across 100+ applications. **All person-specific data lives in `candidate.md`** (same directory) — read it first, every session, and answer every form field from it. To run for a different person, swap `candidate.md`; this engine doesn't change.

## Step 0 — load the candidate
1. Read `candidate.md`. It supplies: identity & contact, résumé file paths + selection rule, education, profile bullets (for essays), **work authorization**, and targeting (location / seniority / comp).
2. If a critical/identifying field needed by a form isn't in `candidate.md` (contact detail, legal-name spelling, work-auth specifics, address, account ID) → **ASK the user, don't guess.** Wrong contact info has gone out on real applications before.
3. **Work authorization is the most candidate-specific field** — never carry one person's visa answers to another. Always map the form's phrasing to the `Work authorization` block in `candidate.md` (a US citizen / GC / "needs sponsorship now" person answers very differently from an OPT holder).

## Targeting & honesty rules (generic logic; thresholds come from candidate.md)
- **Location:** honor the candidate's location constraints. Skip roles outside them (wrong metro, non-US, ITAR/citizenship-required).
- **Seniority / years gates — the key filter:**
  - **Hard gate (SKIP):** a stated minimum ("5+/6+/7+ years") in qualifications, or a Senior/Staff/Principal title above the candidate's level → don't apply, don't fabricate years.
  - **Soft preference (OK to apply):** "you may be a good fit if you have N years" with **no gating question on the form**, when domain fit is strong and the company says "apply even if you don't meet every qualification." Apply with the honest résumé; if there's an essay, name the gap plainly and lean on domain fit.
  - Mid-level / "II" / "2+ yrs" / unleveled "Software Engineer" / "new grad–N yrs" → fits.
- **Never lie on a screening gate.** If a form hard-asserts something untrue for this candidate (years, citizenship-only, no-sponsorship), skip the role.
- **EEO / voluntary self-id** (gender, race, veteran, disability, LGBTQ+) → **decline** ("I don't wish to answer" / "Decline to self-identify").
- **Don't spray same-company.** A few genuinely-distinct, well-fit roles per company max. Respect reapply limits (below).
- **"Why <company>?" essays:** 200–400 words, honest, mapped to the JD. Open with genuine why-this-team, tie 2–3 concrete profile projects to their bullets, close on mission + agency. Tailor per role; don't reuse across roles at one company.

## Finding roles — board scanning
- **Greenhouse (fastest):** `https://job-boards.greenhouse.io/<org>`. From a page already on that origin you can `evaluate_script` + `fetch` other orgs' boards (same-origin, no CORS) and parse `a[href*="/jobs/"]`; paginate `?page=N` (50/page; total shows as "N jobs"). `404` = org not on Greenhouse. Filter titles by keyword (`backend|platform|infrastructure|full-?stack|software engineer|distributed|data engineer|api`), exclude senior (`staff|senior staff|principal|director|manager|lead|intern`), then fetch each JD to read the years gate before deciding.
- **Ashby:** `https://jobs.ashbyhq.com/<Org>` — can't be cross-origin fetched from Greenhouse; navigate directly.
- **A LinkedIn company-jobs link** the user pastes → open the company's own `/careers` page to find the real ATS board (LinkedIn is just a mirror).

## ATS form playbooks (Chrome DevTools MCP)

### Greenhouse (`job-boards.greenhouse.io`)
1. Navigate to job URL; click **Apply**.
2. **Text fields** (name/email/phone/earliest/LinkedIn/address/essay) — set via React-native setter to reliably commit controlled inputs:
   ```js
   const set=(id,v)=>{const el=document.getElementById(id);const p=el.tagName==='TEXTAREA'?HTMLTextAreaElement.prototype:HTMLInputElement.prototype;Object.getOwnPropertyDescriptor(p,'value').set.call(el,v);['input','change','blur'].forEach(t=>el.dispatchEvent(new Event(t,{bubbles:true})));};
   ```
   `fill_form` works for plain inputs but **often won't commit `<textarea>`/multiline** — use the setter above or click+`type_text` for those.
3. **react-select dropdowns** (Yes/No, visa, how-heard, EEO, degree) — open the control, click the option **inside that question's own listbox** (`react-select-<questionId>-listbox`). The phone-country picker (`iti-0__country-listbox`, 244 opts) is always in the DOM and is noise — never grab the first `[role=option]`; target the question's listbox by id:
   ```js
   const pick=async(qid,match)=>{const i=document.getElementById(qid);const c=i.closest('.select__control');['mousedown','mouseup','click'].forEach(t=>c.dispatchEvent(new MouseEvent(t,{bubbles:true})));await new Promise(r=>setTimeout(r,150));const lb=document.getElementById('react-select-'+qid+'-listbox');const o=[...lb.querySelectorAll('[role=option]')].find(x=>x.textContent.trim()===match)||[...lb.querySelectorAll('[role=option]')].find(x=>x.textContent.trim().startsWith(match));o.dispatchEvent(new MouseEvent('mousedown',{bubbles:true}));o.dispatchEvent(new MouseEvent('click',{bubbles:true}));};
   ```
4. **Country field:** open, type `United States`, select **"United States +1"** (shows as `+1`). If submit errors "Select a country", re-open/re-select and resubmit (stale error text can persist).
5. **School/degree/discipline typeaheads:** degree is a fixed list; school is a typeahead — type the candidate's FULL school name and match an exact prefix (partial strings mis-match similarly-named schools).
6. **Résumé:** `upload_file` on the "Attach" button uid (`value="No file chosen"`) from a fresh snapshot.
7. **Submit**, then verify: success = URL ends in **`/confirmation`** and/or page shows **"Thank you for applying"**. The first check can fire before the redirect — re-check URL/heading before reporting failure.
8. reCAPTCHA is usually invisible (v3) and passes silently; a *visible* challenge = blocker.

### Ashby (`jobs.ashbyhq.com`)
- Apply lands on `…/application`. Fields: Name, Email, Resume (upload), Phone, Location, + custom questions.
- **Yes/No are button pairs** — click "Yes" once. Clicking the same button twice toggles it OFF (caused validation errors) — verify, don't re-click.
- **Multiline/essay textareas:** `fill_form` frequently does NOT commit them ("Missing entry for required field" on submit) — click + `type_text`, then resubmit.
- **Location field:** sometimes plain free-text (type "San Francisco, CA" — works), sometimes a **Google-Places autocomplete that returns NO options under automation** (systematic blocker → "Missing entry … Location"). If Places-type: fill everything else, hand back ("user picks location + submits").
- **"How did you hear" combobox:** click, `type_text` to filter, click the matching option.
- Verify success: page text **"Your application was successfully submitted."** (URL may stay on `…/application`).

### Lever / Workday / FAANG
- Lever (`jobs.lever.co/<org>`): standard fields, usually submittable.
- Workday / Eightfold / FAANG portals: **login/account-gated → can't auto-submit.** Deliver a shortlist + tailored answer bank, or open tabs for self-apply.

## Known blockers → hand back to user (fill everything else first)
- Ashby Google-Places location autocomplete (no options under automation).
- Greenhouse 8-char emailed verification codes.
- CAPTCHAs: visible reCAPTCHA, hCaptcha, Gem letter-CAPTCHA, Cloudflare/Dover.
- Login-gated portals (FAANG, Workday).
- Email-only postings → draft the email for the user to send.
Leave the form fully filled; tell the user the single remaining action.

## Reapply limits seen (don't duplicate)
Hightouch: one application per candidate. EliseAI / Opal: 30-day. Braintrust: 90-day. Runloop: flags same-day duplicates.

## Tracking
Maintain a CSV tracker (e.g. `~/Desktop/Job_Applications_Tracker.csv`). Columns: `Company, Role, Domain, Location, Platform, Resume, Status, Action Needed, Date Applied, Link / Notes`. Append a row per application. Status: `Submitted`, `NEEDS YOU` (blocker), `EMAIL DRAFT`, `SELF-APPLY`, `SKIPPED`.

## Per-application checklist
1. Read `candidate.md` (if not already this session).
2. Read JD; confirm location fit + read the **years gate** (skip hard gates).
3. Pick résumé per candidate.md's selection rule.
4. Apply → upload résumé → fill contact fields → essays (setter/type_text) → dropdowns (Yes/No, **work-auth mapped from candidate.md**, EEO-decline, how-heard) → country.
5. Snapshot-verify required fields committed (watch multiline + country).
6. Submit → confirm success (`/confirmation` or "successfully submitted").
7. Append to tracker. If blocked, record the exact remaining user action.
