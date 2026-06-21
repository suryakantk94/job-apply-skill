# Candidate Profile — {{FULL_NAME}}

This is the active candidate profile the `job-apply` engine reads. To run the skill
for a different person, replace this file (same field structure) — the engine in
SKILL.md stays unchanged. Keep one profile active at a time.

> **Setup:** copy this file to `candidate.md` in the same folder, fill in every
> `{{TOKEN}}`, then delete the template or keep it as a reference. **`candidate.md`
> holds personal data — keep it out of version control** (see the repo `.gitignore`).

## Identity & contact (use EXACTLY — from résumé, NOT any session/work email)
- Full legal name: **{{FULL_NAME}}**  (First: `{{FIRST_NAME}}`, Last: `{{LAST_NAME}}`)
- Name you go by: `{{PREFERRED_NAME}}`
- Email: `{{PERSONAL_EMAIL}}`
- Phone: `{{PHONE}}`
- LinkedIn: `{{LINKEDIN_URL}}`
- GitHub: `{{GITHUB_URL}}`
- Home location / work-from address: `{{HOME_CITY_STATE}}`. Use `{{ALT_WORK_CITY}}` for roles asking where you'd work from in another metro.

## Résumés (pick by role)
- **Default (backend / SWE / product / applied-AI):** `{{RESUME_DEFAULT_PATH}}`
- **AI/ML-heavy (ML / CV / perception / research / "complex AI"):** `{{RESUME_AIML_PATH}}` — {{RESUME_AIML_NOTES}}
- Selection rule: backend/platform/full-stack/data-infra → default. ML/perception/research → AI/ML. If the user names a résumé for a company, honor it for that whole company.

## Education
- {{UNIVERSITY_GRAD}} — **{{DEGREE_GRAD}}**.
- {{UNIVERSITY_UNDERGRAD}} — {{DEGREE_UNDERGRAD}}.
- Watch the school typeahead: type the FULL school name (partial strings mis-match similarly-named schools).

## Profile (for essays — keep honest)
{{TITLE}} **~{{YEARS_EXPERIENCE}} YOE** at {{CURRENT_COMPANY}} ({{COMPANY_DESCRIPTION}}). Stack: {{TECH_STACK}}.
Shipped: {{KEY_PROJECTS}}.
{{THROUGHLINE}} — lean on it for related roles.

## Work authorization  ← the field most likely to differ per person; answer forms from THIS
- Status: **{{WORK_AUTH_STATUS}}**.
- "Authorized to work in the US now?" → **{{AUTHORIZED_NOW}}**.
- "Require sponsorship now or in future?" → **{{NEEDS_SPONSORSHIP}}**.
- "Authorized to work for any employer?" → **{{ANY_EMPLOYER}}**.
- Option-style ("describe your work authorization") → pick the option that matches {{WORK_AUTH_STATUS}}; if an option names your visa explicitly, pick that.
- Free-text sponsorship question → *"{{WORK_AUTH_FREETEXT}}"*

## Targeting
- **Location:** {{LOCATION_CONSTRAINTS}}. Skip non-US, ITAR/citizenship-required.
- **Seniority:** ~{{YEARS_EXPERIENCE}} YOE. Apply to: unleveled "Software Engineer", "II", mid-level, "new grad–N yrs", "2+ years". Skip: Staff/Senior Staff/Principal/Director/EM/Manager/VP/Lead, and any hard "5+/6+/7+ years" gate.
- **Comp expectation:** *"{{COMP_RANGE}}"*
- **Start date:** *"{{START_DATE}}"*

## Misc standard answers
- In-office / hybrid / X days a week → **{{IN_OFFICE}}**.
- Open to relocation → **{{RELOCATION}}**.
- Interviewed here before → **{{INTERVIEWED_BEFORE}}**.
- Interview-language preference → **{{INTERVIEW_LANGUAGE}}**.
- "How did you hear about us?" → **{{HOW_HEARD}}**.
- Candidate-AI-use acknowledgement → **{{AI_USE_ACK}}**.
