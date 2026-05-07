# Duet Books — Deployment & GitHub

## GitHub Setup
- **Account**: duetbooksapp
- **Repo**: github.com/duetbooksapp/duetbooks
- **Live URL**: https://duetbooksapp.github.io/duetbooks
- **Branch**: main
- **Local repo**: /Users/steve/Desktop/DuetBooks/

## How to Push Changes
The git remote has the auth token embedded in the URL, so pushing works directly:
```bash
cd /Users/steve/Desktop/DuetBooks
cp DuetBooks.html index.html
git add index.html DuetBooks.html
git commit -m "description"
git push origin main
```
GitHub Pages auto-deploys within ~30 seconds after push.

## gh CLI Location
`/tmp/gh_install/gh_2.65.0_macOS_arm64/bin/gh` — downloaded directly (no Homebrew on this machine). Authenticated as duetbooksapp account.

NOTE: The macOS Keychain has smaxim-spec credentials that override git's credential helper. The workaround is the token embedded in the remote URL.

## Local Preview
- Launch config in `.claude/launch.json` serves from `/tmp/duetbooks/` on port 8099 using Ruby
- After editing DuetBooks.html, copy to: `cp DuetBooks.html /tmp/duetbooks/DuetBooks.html`
- Preview tool may cache — restart server if changes don't appear

## Firebase
- URL: `https://duet-crm-default-rtdb.firebaseio.com/duetbooks/`
- Each agent gets their own namespace: `/duetbooks/{agent_id}/`
- No authentication on Firebase (public read/write) — relies on agent PIN for app-level auth
- Data: cases, payperiods, settings, profile

## File Structure
```
/Users/steve/Desktop/DuetBooks/
  DuetBooks.html        — Source file (edit this)
  index.html            — Production copy (auto-copied from DuetBooks.html)
  DOCUMENTATION.md      — Comprehensive docs
  DuetBooks_backup_*.html — Timestamped backups
  .claude/
    CLAUDE.md           — Project instructions
    memory/             — Context files for future conversations
  .git/                 — Git repo linked to GitHub
```
