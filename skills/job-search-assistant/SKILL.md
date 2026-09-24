---
name: job-search-assistant
description: Tailors a resume, matches the user to open roles, writes cover letters, and keeps a local applications dashboard updated. Use this skill when the user wants help finding a job, rewriting or tailoring a resume/CV, writing a cover letter, tracking job applications, or asks things like "help me find a job", "ajuda a procurar emprego", "adapta meu currículo", "escreve uma carta de apresentação", "busca de emprego".
---

# Job Search Assistant

Helps someone through a job search from start to finish: tailor the resume, find matching roles, write a cover letter for each one, and keep a dashboard of what was applied to.

**Language:** this file is written in English, but always talk to the user in whatever language they're using in the conversation (Portuguese, Spanish, English, anything else). Translate the example lines below into that language rather than repeating them in English.

## Tone

When writing or rewriting resume or cover letter content, write the way a real person would. Avoid:
- Corporate filler like "highly motivated professional" or "leverage synergies"
- Generic buzzwords that don't say anything specific
- Em dashes and other tells of AI-written text

Use direct, confident, natural language. Job searching is stressful; be a helpful second pair of eyes, not a robot filling out a form.

## Where things live

All generated files go under a fixed folder in the user's home directory, `~/.job-search-assistant/` (resolve `~` to the actual home directory for the OS this session is running on: `$HOME` on macOS/Linux, `%USERPROFILE%` on Windows), so they persist no matter which project folder Claude Code was launched from:

```
~/.job-search-assistant/
├── vagas.json            # applications data, the source of truth for the dashboard
├── painel-vagas.html     # the dashboard itself, regenerated from vagas.json
└── resumes/
    └── <company>-<role>/
        ├── resume.docx
        └── cover-letter.docx
```

Create this folder the first time it's needed. Never ask the user to manage these paths themselves, just use them.

## Step 1: Collect the resume

Ask the user to share their resume or CV, pasted as text or uploaded as a file.

## Step 2: Understand what they're looking for

After reading the resume:
1. Identify their background, skills, and experience level.
2. Suggest 2 to 4 role categories that fit that profile.
3. Ask them to confirm or redirect: are these the right roles, or do they already have something specific in mind?

## Step 3: Tailor the resume

Once the target role is clear, rewrite the resume so it:
- Leads with the experience and skills most relevant to that role
- Reads naturally, not like AI-generated filler
- Keeps a clean, easy-to-scan structure
- Has a summary/objective line that speaks directly to the target role

Build it as a `.docx` using `scripts/generate_docx.py` (see "Generating documents" below), save it to `~/.job-search-assistant/resumes/<company>-<role>/resume.docx`, and share that file with the user. Then ask if they want changes before moving on to the job search.

## Step 4: Search for job opportunities

Search the web (LinkedIn, Indeed, Glassdoor, company career pages, etc.) for open roles matching the target profile.

For each match, present:
- Job title and company
- Location or remote status
- A match score from 0 to 100, worked out individually for that specific posting by comparing its requirements against the resume. Never reuse one score across different jobs.
- A short note on why it's a good fit, or where it falls short if the score is low
- A direct link, if one exists

## Step 5: Offer a cover letter per job

For each opportunity, ask if they want a cover letter for it. If yes, write one that:
- Is specific to that job and company, not a template
- Sounds like a real person wrote it: direct, confident, genuine
- Connects specific points from the job description to the user's actual experience

Build it as a `.docx` with `scripts/generate_docx.py`, save it to `~/.job-search-assistant/resumes/<company>-<role>/cover-letter.docx`, and share it.

## Step 6: Keep the dashboard updated

Every time a resume and/or cover letter gets generated for a job (meaning the user applied to it through this skill), record it automatically. Don't wait to be asked.

1. Read `~/.job-search-assistant/vagas.json` (an empty array `[]` if it doesn't exist yet).
2. Append a new entry with a stable `id` (a slug of company + role + date, so re-runs never duplicate it), and:
   - `cargo` (role), `empresa` (company), `data` (`YYYY-MM-DD`), `score` (that job's own match score from Step 4), `status: "Aplicado"`, `comentario: ""`, `link` (if available)
   - Never overwrite an existing entry's `status` or `comentario`. Those belong to the user's own tracking and only change when the user asks, through conversation (there's no edit form in the dashboard itself, see below).
3. Write the updated array back to `vagas.json`.
4. Regenerate the dashboard: read `assets/painel-template.html` from this plugin, replace the exact text `__VAGAS_JSON__` with the current contents of `vagas.json`, and write the result to `~/.job-search-assistant/painel-vagas.html`.
5. Tell the user where the dashboard file is so they can open it (it's a plain local file, double-click to open in a browser, no server needed).

The dashboard is view-only. There's no in-browser form because a local file opened straight from disk can't write back to itself. Any change (mark a job as "Entrevista", add a comment, log an application the user made outside this skill, remove an entry) happens by asking Claude, which updates `vagas.json` and regenerates the HTML the same way.

## Generating documents

Both resume and cover letter go through `scripts/generate_docx.py` (bundled with this plugin, uses `python-docx`; the script installs that dependency itself on first run if it's missing, no setup needed).

**Resume:**
```
python scripts/generate_docx.py resume --data <path-to-resume.json> --output <path-to-resume.docx>
```
Data file shape:
```json
{
  "name": "Full Name",
  "contact": "email · phone · city · linkedin.com/in/handle",
  "summary": "Two or three sentences tailored to the target role.",
  "sections": [
    {
      "heading": "Experience",
      "entries": [
        {
          "title": "Role title",
          "subtitle": "Company · Jan 2022 - Present",
          "bullets": ["Specific, concrete accomplishment.", "Another one."]
        }
      ]
    },
    {
      "heading": "Education",
      "entries": [
        {"title": "Degree", "subtitle": "School · Year", "bullets": []}
      ]
    },
    {
      "heading": "Skills",
      "bullets": ["Skill one", "Skill two"]
    }
  ]
}
```

**Cover letter:**
```
python scripts/generate_docx.py letter --data <path-to-letter.json> --output <path-to-letter.docx>
```
Data file shape:
```json
{
  "sender_name": "Full Name",
  "sender_contact": "email · phone · city",
  "date": "September 23, 2026",
  "recipient": "Hiring Manager\nCompany Name",
  "salutation": "Dear Hiring Manager,",
  "paragraphs": [
    "Opening paragraph.",
    "Middle paragraph connecting experience to the job.",
    "Closing paragraph."
  ],
  "closing": "Sincerely,",
  "signature_name": "Full Name"
}
```

Write the JSON content yourself based on the tailored resume/letter text, then call the script. Use whichever Python is available on the system (`python` or `python3`).

## Notes

- Always let the user steer. Ask before moving to the next step.
- If they want to apply to more than one job, repeat Steps 4 and 5 as needed.
- Never generate content that sounds robotic or over-polished.
