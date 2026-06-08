# Self-hosting `github-readme-stats` on Vercel

This guide explains how to run your own private instance of
[`anuraghazra/github-readme-stats`](https://github.com/anuraghazra/github-readme-stats)
on Vercel so you can add **reliable** live GitHub stats and top-languages cards to
your profile README (`EmadMokhtar`).

## Why self-host?

The public instance at `github-readme-stats.vercel.app` is shared by millions of
users and a single GitHub Personal Access Token (PAT). As a result it is
**chronically rate-limited** and frequently returns **HTTP 503** errors — the cards
render as broken images on your profile. (The trophy service has a similar problem and
returns **402**.)

Running your own instance on Vercel, backed by **your own** GitHub PAT, gives you a
dedicated rate-limit budget. The cards then load reliably every time. The whole thing
runs on Vercel's free Hobby tier and costs nothing.

> This is only needed for the **stats card** and the **top-languages card** — see
> [What is NOT affected](#what-is-not-affected) at the bottom.

---

## Step-by-step

### 1. Fork the repository

1. Go to <https://github.com/anuraghazra/github-readme-stats>.
2. Click **Fork** (top-right) to create `EmadMokhtar/github-readme-stats`.

### 2. Create a GitHub Personal Access Token (PAT)

You need a token so your instance can call the GitHub API on your behalf. Use the
**minimal** scopes below — anything more is unnecessary.

**Option A — Classic token (simplest, recommended by the docs):**

1. Go to **GitHub → Settings → Developer settings → Personal access tokens →
   Tokens (classic)**.
   Direct link: <https://github.com/settings/tokens>
2. Click **Generate new token → Generate new token (classic)**.
3. Give it a name (e.g. `github-readme-stats`) and an expiration.
4. Select **only** these two scopes:
   - `repo`
   - `read:user`
5. Click **Generate token** and **copy the value now** — you cannot see it again.

**Option B — Fine-grained token (more locked-down):**

1. Go to **GitHub → Settings → Developer settings → Personal access tokens →
   Fine-grained tokens → Generate new token**.
2. Under **Repository permissions**, grant **read-only** access to:
   - Commit statuses
   - Contents
   - Issues
   - Metadata
   - Pull requests
3. Generate and copy the token.

> Note: the fine-grained option limits the scope to issues in your repositories and
> includes only public commits by default.

### 3. Import the fork into Vercel

1. Go to <https://vercel.com> and **Log in with GitHub** (authorize Vercel to access
   your repositories if prompted).
2. In the Vercel dashboard, click **Add New… → Project**.
3. Choose **Continue with GitHub**, find your fork
   `EmadMokhtar/github-readme-stats`, and click **Import**.

### 4. Add the PAT as an environment variable

This is the critical step.

1. On the import / project-configuration screen, expand **Environment Variables**.
2. Add a variable with this **exact name**:
   - **Name:** `PAT_1`   ← the env var name is literally `PAT_1` (verified from the
     official repo README)
   - **Value:** the token you copied in Step 2.
3. (You only need `PAT_1`. Multiple tokens — `PAT_1`, `PAT_2`, … — are only useful for
   very high-traffic instances, which a personal profile is not.)

> If you ever see the error *"Maximum retries exceeded. Please add an env variable
> called `PAT_1` with your github token in vercel"* on a card, it means `PAT_1` is
> missing, misspelled, or the token is expired/invalid.

### 5. Deploy

1. Click **Deploy**.
2. When the build finishes, Vercel assigns you a domain such as
   `https://github-readme-stats-emadmokhtar.vercel.app` (the exact subdomain depends
   on the project name you chose).
3. Open `https://<your-app>.vercel.app/api?username=EmadMokhtar` in a browser to
   confirm the stats card renders. If you see a card image, it works.

### 6. (Optional) Updating later

Your fork won't auto-update. To pull in upstream fixes, periodically use GitHub's
**Sync fork** button on `EmadMokhtar/github-readme-stats`; Vercel will redeploy
automatically on the new commit.

---

## Local `vercel` CLI?

The Vercel CLI is **not installed** on this machine (`which vercel` found nothing, and
there is no authenticated session). The official `github-readme-stats` documentation
also only describes the **web import flow** (dashboard) — there is no documented CLI
deploy path.

**Recommendation: use the web import flow above.** It's the supported, documented
method and requires no local tooling.

If you'd prefer the CLI anyway, you could install it with `npm i -g vercel`, run
`vercel login`, then `vercel` / `vercel --prod` from inside a clone of your fork, and
add `PAT_1` via `vercel env add PAT_1`. But the web flow is simpler and is what's
documented, so it's the way to go.

---

## README snippet (ready to paste)

Replace `https://YOUR-APP.vercel.app` with the domain Vercel gave you in Step 5
(e.g. `https://github-readme-stats-emadmokhtar.vercel.app`). The theme matches the
existing streak card: `tokyonight`, `hide_border=true`, `bg_color=0d1117`, accent
`58a6ff`.

```markdown
<img src="https://YOUR-APP.vercel.app/api?username=EmadMokhtar&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff&show_icons=true" alt="GitHub stats" />

<img src="https://YOUR-APP.vercel.app/api/top-langs/?username=EmadMokhtar&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff&layout=compact&langs_count=8" alt="Top languages" />
```

### Where to paste it

In `README.md`, under the **`## 📊 GitHub activity`** section, **inside the existing
`<div align="center">`**, alongside the streak card (the
`streak-stats.demolab.com` `<img>`). The result should look like this:

```markdown
## 📊 GitHub activity

<div align="center">

<img src="https://streak-stats.demolab.com?user=EmadMokhtar&theme=tokyonight&hide_border=true&background=0d1117&ring=58a6ff&fire=58a6ff&currStreakLabel=58a6ff" alt="GitHub streak" />

<img src="https://YOUR-APP.vercel.app/api?username=EmadMokhtar&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff&show_icons=true" alt="GitHub stats" />

<img src="https://YOUR-APP.vercel.app/api/top-langs/?username=EmadMokhtar&theme=tokyonight&hide_border=true&bg_color=0d1117&title_color=58a6ff&icon_color=58a6ff&layout=compact&langs_count=8" alt="Top languages" />

</div>
```

> Tip: to count private contributions in the stats card, append
> `&count_private=true` (requires the `repo` scope, which the classic token above
> already has).

---

## What is NOT affected

Only the stats card and top-languages card need the self-hosted instance. These other
widgets in the README use **independent services** and keep working as-is — no change
needed:

- **Streak card** — `streak-stats.demolab.com`
- **Capsule render** header/banner — `capsule-render.vercel.app`
- **Typing SVG** — `readme-typing-svg.demolab.com`
- **Shields.io** tech badges — `img.shields.io`

---

## Quick reference

| Item | Value |
| --- | --- |
| Repo to fork | `anuraghazra/github-readme-stats` |
| Env var name | `PAT_1` |
| Classic PAT scopes | `repo`, `read:user` |
| Fine-grained perms (read-only) | Commit statuses, Contents, Issues, Metadata, Pull requests |
| Stats card path | `/api?username=EmadMokhtar` |
| Top-langs card path | `/api/top-langs/?username=EmadMokhtar` |
| Deploy method | Vercel web import (no CLI installed; no CLI flow documented) |
