---
name: uk-application-sprint
description: Scheduled every 2 hours during the day. Sweeps UK job boards, chooses roles with a written role-selection prompt, builds a tailored CV and cover letter per role, tests them, and stages a named batch. Unattended: never submits.
---

You are running the user's UK APPLICATION SPRINT. This is an unattended run: find new UK roles worth applying to,
prepare a complete tailored application for each, and stage them as a named batch. **Unattended runs never submit an
application, never send anything, never create an account, never enter a password, never solve a CAPTCHA.**
Submitting happens in a live session after the user says "apply" / "submit batch N".

PROJECT ROOT: the jobs project folder.

## 0. Load (every run starts with no memory). Read in full, in this order:
1. The application master prompt (which file governs what, CV method, cover letters, tests, form answers, submitting
   mechanics, authorisation, records).
2. `canonical-profile.md` INCLUDING every dated correction at the bottom (later corrections win). The only source of
   facts.
3. The role-selection prompt (gates, 0-100 application-priority score, add-ons, thresholds). It governs selection.
4. The CV and cover-letter prompts. They govern the documents.
5. `job-search-state.json`: never re-apply to anything APPLIED or READY; a DROPPED role may be reconsidered only when
   the posting, the evidence, the rules or a factual correction changed (say which).
6. The newest batch file so numbering continues.
Start the run log with the list of files read.

## 1. Discover (at least three source families; log raw counts)
- SCOPE: only the role families the rulebook allows. Drop anything whose essentials the user cannot truthfully meet.
- ATS APIs: `python3 tools/sweep.py "<ashby slugs>" "<greenhouse slugs>" "<lever slugs>" sweep.json` with the slug
  lists in `tools/boards.json`, then `python3 tools/filt.py sweep.json`. Try 20+ new UK company slugs per run and add
  those that resolve to `tools/boards.json`.
- YC Work at a Startup, and LinkedIn public search (no login) as lead sources; confirm every lead is live on the
  employer's own site or ATS before it counts.
- De-duplicate against the tracker BEFORE reading, then read every new plausible ad in full and save each JD kept.
A source returning zero is a broken instrument: say so; never report it as an empty market.

## 2. Choose roles: follow the role-selection prompt exactly
- Gates: experience ceiling; no confirmed gap in an essential; no unmet minimum-qualification cut-off in the vacancy's
  own criteria (checked per vacancy); no clearance; no stated requirement the user cannot truthfully meet; live on the employer's site
  today and the company trading; anywhere in the UK; base pay not below the user's floor.
- Application-priority score 0-100 as a breakdown (a local heuristic, not a probability): work fit 35, estimated
  evidence coverage 30 (a checklist from the JD against the profile BEFORE any CV is built), level 15, requirements fit 10
  (the employer's stated requirements against the profile), role preference 4, pay 3, long-term value 3. Add-ons shown separately: warm
  route +10, AI-forward job +5.
- 80+ = build and stage for APPLY. 65-79 = build nothing, list as ASK. Below 65 = drop, unless it is exactly a dream
  role family, then ASK.
- Overrides to ASK: fixed-term or contract roles; any fact only the user holds.

## 3. For each APPLY role
1. Freeze the criteria checklist from the JD (a fresh agent that sees only the JD).
2. Build the CV from that JD and the profile: one page, 95-97% fill measured on the rendered file, British English,
   no first-person bullets.
3. Build the cover letter, always, even if optional.
4. Run the tests: `tools/ats_check.py` and the CV parse test must print RESULT ALL PASS; measured evidence coverage
   against the frozen checklist; a blind screener as a red-flag check; an honesty audit of the CV AND letter against
   the profile (separate agent). Fix true findings and rerun.
5. SECOND FIT CHECK: measured coverage 8/10 or more = stage for APPLY; 7 to under 8 = ASK (recommend whether to
   send); below 7, more than 2 points below the estimate, or a gap in an essential = ASK with a do-not-send
   recommendation.
6. Draft every form answer from the profile's standard answers. Never guess an answer only the user can give
   truthfully: flag it for the user.

## 4. Stage the batch (never submit)
Write `sprint-batch-<N>-form-answers.md`: per role, company, role, URL, score breakdown with add-ons, estimated and
measured coverage, pay with source and date or UNKNOWN, honest gaps, CV and letter paths with SHA-256,
every form answer, and any step only the user can do (login, CAPTCHA, emailed code). Add APPLY roles to the tracker
as READY with `next_action: "Awaiting user: submit batch N"`; add ASK roles with the reason; add every dropped role
to `evaluated` with its gate or reason. If nothing qualified, say "No new qualifying roles this run" with the raw
counts.

## 5. Report
A short message readable on a phone: batch number; each APPLY role (company, role, score, pay, one-line case); the
ASK list with why; the counts (postings seen, new after de-dupe, descriptions fetched, machine-screened, model-read);
and "Reply 'submit batch N' in a live session to send." List READY roles older than 24 hours so they are not
forgotten.

APPLICATION BOARD: if this run changed any status, rebuild and republish the application board. Say whether it was
updated or BLOCKED.
