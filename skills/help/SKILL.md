---
name: help
description: Explains what the Job Search Assistant plugin does and how to use it, step by step. Use this when the user asks how this skill works, what it does, "como uso essa skill", "o que essa skill faz", "help", "ajuda", or invokes it directly as /job-search-assistant:help.
---

# How to use Job Search Assistant

Answer in whichever language the user is using. Below is the reference content in English; translate it on the fly rather than repeating it verbatim in English for a Portuguese (or other language) conversation.

## What it does

One skill covers the whole job search:
1. Tailors your resume to a specific role
2. Searches the web for matching openings and scores each one against your resume
3. Writes a cover letter for any job you want to apply to
4. Keeps a dashboard of every application, saved locally on your machine

## How to start

Just say what you want, in your own words. A few examples that work:
- "Help me find a job as a product manager"
- "Here's my resume, can you tailor it for backend roles?"
- "Adapta meu currículo para vagas de marketing"
- "Write me a cover letter for this job: <link or description>"

There's no required command or fixed phrasing. The main skill (`job-search-assistant`) picks up on any of these and walks through the flow: resume first, then matching roles, then a cover letter per job you want to apply to.

## Where your files end up

Everything lands in `~/.job-search-assistant/` in your home folder, no matter which project folder you're running Claude Code from:
- `resumes/<company>-<role>/resume.docx` and `cover-letter.docx` for each job
- `painel-vagas.html`, a dashboard of every application, plain HTML you can open directly in a browser
- `vagas.json`, the data behind that dashboard

## Updating the dashboard

The dashboard updates itself whenever a resume or cover letter gets generated for a job. To change anything else, just ask: "mark the Acme interview as done", "add a comment on the Globex application", "I applied to this one manually, add it too". There's no edit form inside the dashboard file itself, since a plain local HTML file can't save changes back to disk. Ask, and the change gets written for you.
