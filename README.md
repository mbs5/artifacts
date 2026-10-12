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
3. Redeploy:
   ```
   python3 ~/workspace/skills/vercel/bin/deploy.py ~/workspace/artifact-library --name artifacts --target production --wait
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

## GitHub auto-deploy (standing rule — wiring PENDING as of 2026-10-07)

The standing rule is every Vercel deploy goes through a linked GitHub repo
(push → auto-deploy), never deploy-by-upload as the permanent setup. This
project is still deploy-by-upload (`link: null` on the Vercel project) — the
Oct 4–7 H-1B briefing updates were saved locally but the live page stayed
stale twice because background-run writes (repo creation) are denied.

To wire it up (run in a normal turn, not a background run):
1. Create the repo with a small Python snippet (gh-api takes no request
   body), e.g.:
   ```python
   import sys, json, urllib.request
   sys.path.insert(0, "/opt/hatch/skills/skill-creator/bin")
   from dynamic_credentials import add_surrogate_to_request
   data = json.dumps({"name":"artifact-library","private":True}).encode()
   r = urllib.request.Request("https://api.github.com/user/repos", data=data,
       headers={"Accept":"application/vnd.github+json","Content-Type":"application/json"}, method="POST")
   add_surrogate_to_request(r, "custom.github", entry_name="access_token", allowed_hosts=["api.github.com"])
   repo = json.load(urllib.request.urlopen(r))
   print(repo["id"], repo["full_name"])  # capture the id
   ```
2. Push: `~/workspace/skills/github/bin/gh-push mbs5/artifact-library ~/workspace/artifact-library --branch main`
3. Link: `POST /v9/projects/prj_imw9crO4pbrIjoLuNJcy7wdIBhoK/link`
   with `{"type":"github","repo":"mbs5/artifact-library","repoId":<id>}`
   (undocumented endpoint `vercel git connect` uses; `PATCH` rejects
   `link`/`gitRepository`). Requires the Vercel GitHub app installed — it is.
4. Push again (step 2) after linking so Vercel auto-deploys; verify the live
   page (e.g. https://artifacts.closedloopintel.com/jobs-vs-internships/)
   shows the new content.
5. After this is done, replace step 3 of "How to add an artifact" with:
   `gh-push mbs5/artifact-library ~/workspace/artifact-library` (Vercel
   auto-deploys on push).
