
# Auto LinkedIn (100% Free Stack)

Research topics via Google News RSS, draft LinkedIn-style posts with a lightweight extractive summarizer (no paid LLM), and optionally auto-post via LinkedIn's **official Posts API**. Runs on **GitHub Actions** for free.

> No paid APIs. No scraping logins. Just RSS + public web pages + extractive summarization.

## What you get
- **Research**: pulls top recent articles for each topic from Google News RSS, fetches pages, extracts main text with Readability.
- **Summarize**: simple extractive summary + keyphrases → LinkedIn-friendly post (hook + bullets + CTA + hashtags + sources).
- **Post**: optional auto-post to your profile via LinkedIn Posts API (`/rest/posts`). Uses OAuth access token you supply.
- **Schedule**: GitHub Actions cron (weekdays at 08:15 Europe/London).

## Quick start
1. **Clone / upload** this repo to GitHub.
2. Put your topics into `topics.json` (examples included).
3. If you want **auto-posting**:
   - Create a LinkedIn developer app and self-authorize your account with scope `w_member_social`.
   - Retrieve an **access token** (and ideally a refresh token in your own small service; for a quick start you can paste a long-lived token).
   - In GitHub → Repo Settings → **Secrets and variables → Actions**:
     - Add `LINKEDIN_ACCESS_TOKEN` with your token.
   - Toggle posting by setting `POST_MODE=post` (see notes below).
4. By default the workflow **runs in dry-run** (no posting). You’ll see drafted posts in the job logs and `dist/` artifacts.

## Configure topics
Edit `topics.json` (array of strings) or `topics.yaml` (more fields). The runner uses `topics.json` by default.

## Modes
- **dry** (default): drafts only, prints to console, writes to `dist/`.
- **post**: creates a real LinkedIn post via `rest/posts`.
- Set via env var `POST_MODE`: `dry` or `post`.

## LinkedIn setup (free)
- Create an app at https://www.linkedin.com/developers/ .
- Request `w_member_social` (for profile). For a Company Page, also ensure you’re a Page Admin and use the Org URN author instead.
- Obtain an OAuth access token with the scope. Store it as `LINKEDIN_ACCESS_TOKEN` in GitHub Secrets.
- The code calls:
  - `GET https://api.linkedin.com/v2/me` → `urn:li:person:{id}`
  - `POST https://api.linkedin.com/rest/posts` with minimal JSON body (text only in this starter).

> For production, implement token refresh. This starter expects a valid token at runtime.

## Run locally
```bash
npm i
POST_MODE=dry node src/run.js
# or actually post (be sure token is set):
LINKEDIN_ACCESS_TOKEN="your_token" POST_MODE=post node src/run.js
```

## GitHub Actions (free)
- Workflow is in `.github/workflows/schedule.yml`.
- It runs at 08:15 (London) on weekdays and on manual dispatch.
- It saves drafts to artifacts (`dist/`).

## Notes
- This uses **Google News RSS** per topic to find fresh sources (no API key).
- The summarizer is extractive + keyphrase-based to stay 100% free.
- If you later want higher quality prose, swap in any LLM API in `writer.js`.
- Images/documents can be added later (requires asset upload endpoints).

## Legal & compliance
- Use the **official LinkedIn API** only.
- Respect robots.txt and website terms when fetching pages.
- Cite 1–3 sources in every post.
