# UK application sprint

Runs every two hours: finds new UK roles worth applying to, builds a tailored, tested CV and cover letter for each, and stages them as a numbered batch for the user to approve.

**Type:** Claude scheduled task · **Area:** Job search automation

## What it does

- Loads the application manual, the profile (with dated corrections) and the role-selection and CV/letter prompts, in a fixed order.
- Discovers roles from company job boards, YC and LinkedIn leads, de-duplicates against the tracker, and reads every new ad in full.
- Scores each role 0-100 as an explained breakdown (work fit, evidence coverage, level, requirements fit, preference, pay, long-term value).
- For each APPLY role: freezes a criteria checklist from the ad, builds a one-page CV and a letter, runs an ATS parse test, measures coverage, runs a blind screener and a separate honesty audit, then re-checks fit with the measured figure.
- Writes a batch file with every form answer and file hash, and a phone-readable report.

## Guardrails

- Unattended runs never submit, send, create accounts, enter passwords or solve CAPTCHAs.
- Any form question only the user can answer truthfully is flagged for them, never guessed.

## When it triggers

> Scheduled every 2 hours during the day. Sweeps UK job boards, chooses roles with a written role-selection prompt, builds a tailored CV and cover letter per role, tests them, and stages a named batch. Unattended: never submits.

## What's in this repo

- `SKILL.md`: the skill itself, the file Claude or Codex reads.
- Not included: The prompts, profile, tracker and build/test scripts are personal and not published.
- Written to be generic: where the skill relies on the user's own files, it names them by role, not by content.

## Install

This is a Claude scheduled-task prompt. Create a scheduled task in the Claude desktop app and paste the body of `SKILL.md` as its prompt, or save it as `~/.claude/scheduled-tasks/uk-application-sprint/SKILL.md`. It expects the project files it names to exist.
