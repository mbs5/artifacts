# Artifact Library

Personal library of artifacts, hosted on Vercel and served at
**https://artifacts.closedloopintel.com**.

## How to add an artifact (any session)

1. Create a folder for it: `<slug>/index.html` (self-contained page; no build step).
2. Add one entry to `artifacts.json`:
   ```json
   {
     "slug": "my-artifact",
     "title": "My Artifact",
     "theme": "Career",
     "description": "One plain sentence on what it is.",
     "path": "/my-artifact/",
     "created": "2026-09-30"
   }
   ```
   Keep `slug`, folder name, and `path` in sync. Themes group cards on the
   home page — reuse an existing theme when one fits (current: `Career`).
3. Deploy (Vercel auto-deploys on push):
   ```
   ~/workspace/skills/github/bin/gh-push mbs5/artifacts ~/workspace/artifact-library --branch main
   ```
   The custom domain stays attached to the project; no DNS change needed.

## Conventions

- Pages are plain static HTML/CSS/JS. System font stack, normal colors, no
  eyebrow labels, no marketing-text flood (user's standing taste).
- The home page (`index.html`) renders cards from `artifacts.json` — never
  hardcode artifact listings into it.
- Every artifact page links back to `/` in its footer.
- Vercel project name: `artifacts` (project id `prj_imw9crO4pbrIjoLuNJcy7wdIBhoK`).
  Domain: `artifacts.closedloopintel.com` (CNAME → `cname.vercel-dns.com`,
  managed in GoDaddy).

## GitHub auto-deploy (standing rule — wired 2026-10-11)

Source lives at github.com/mbs5/artifacts (pushed via `gh-push`); the Vercel
`artifacts` project is linked to it (production branch `main`, repo root is the
site root). Push → auto-deploy. No manual `deploy.py` runs needed.

The standing rule is every Vercel deploy goes through a linked GitHub repo
(push → auto-deploy), never deploy-by-upload as the permanent setup.

To deploy changes (run in a normal user turn — background/proactivity turns
cannot write to GitHub):
```
~/workspace/skills/github/bin/gh-push mbs5/artifacts ~/workspace/artifact-library --branch main
```
Then verify the live page (e.g. https://artifacts.closedloopintel.com/jobs-vs-internships/)
shows the new content. Vercel auto-deploys on every push to `main`.

State as of 2026-10-14: repo `mbs5/artifacts` exists, Vercel project
`prj_imw9crO4pbrIjoLuNJcy7wdIBhoK` is linked to it. The live jobs briefing
already carries the H-1B (Gilliam) update and the OPT-fee content; only the
newest local edits still need a push.

