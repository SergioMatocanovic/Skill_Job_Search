# Job Search Assistant

A free plugin for Claude Code and Claude Cowork that helps with a job search end to end: it tailors your resume for a specific role, searches the web for matching openings, writes a cover letter for each one, and keeps a local dashboard of every application.

Everything runs on your own machine. Resumes and cover letters come out as `.docx` files, and the applications dashboard is a plain HTML file you open straight from disk, no account, no server, nothing to configure.

## What it does

1. You share your resume or CV.
2. It suggests role categories based on your background and confirms the direction with you.
3. It rewrites your resume for the target role and saves it as a Word document.
4. It searches LinkedIn, Indeed, Glassdoor, and company pages for open roles, scoring each one against your resume.
5. For any job you want to apply to, it writes a tailored cover letter.
6. It logs the application in a dashboard (`painel-vagas.html`) so you can see everything you've applied to at a glance.

Works in Portuguese, English, or whatever language you use in the conversation.

## Install

**Claude Code:**
```
claude plugin marketplace add SergioMatocanovic/Skill_Job_Search
claude plugin install job-search-assistant@Skill_Job_Search
```

**Claude Cowork:**

Go to Customize -> Plugins -> Add marketplace, and enter:
```
SergioMatocanovic/Skill_Job_Search
```
then install `job-search-assistant` from the list.

## Using it

Just say what you need, in your own words: "help me find a job as a product manager", "tailor my resume for backend roles", "escreve uma carta de apresentação para essa vaga". There's no fixed command to remember. Ask `/job-search-assistant:help` at any point for a walkthrough.

Generated files live in `~/.job-search-assistant/` in your home folder: resumes and cover letters under `resumes/<company>-<role>/`, and the dashboard as `painel-vagas.html`.

## Requirements

Python 3 needs to be installed and on your PATH, for generating the `.docx` files. The `python-docx` package installs itself automatically the first time it's needed.

## License

MIT, see [LICENSE](LICENSE).
