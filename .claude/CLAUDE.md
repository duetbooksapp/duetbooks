# Duet Books — Project Instructions

## Overview
Duet Books is a standalone AAA Life Specialist Compensation Tracker. Single HTML file (`DuetBooks.html`) with Firebase backend, deployed to GitHub Pages.

## Key Rules
- **Never touch `actualCommission`** — tier/3MMA is a parallel system, commission from Varicent imports is sacred
- **Always create a backup** before making code changes: `cp DuetBooks.html DuetBooks_backup_$(date +%Y%m%d_%H%M%S).html`
- **Copy to index.html** after changes: `cp DuetBooks.html index.html`
- **Push to GitHub** after changes using the configured git credentials
- **Copy to /tmp/duetbooks/** for local preview server: `cp DuetBooks.html /tmp/duetbooks/DuetBooks.html`

## Deployment
- **GitHub repo**: `github.com/duetbooksapp/duetbooks` (account: duetbooksapp)
- **Live site**: `duetbooksapp.github.io/duetbooks`
- **Git credentials**: Token embedded in remote URL, gh CLI at `/tmp/gh_install/gh_2.65.0_macOS_arm64/bin/gh`
- **Firebase**: `https://duet-crm-default-rtdb.firebaseio.com/duetbooks/{agent_id}/`
- **Preview server**: Launch config serves from `/tmp/duetbooks/` on port 8099

## Tech Stack
- Single HTML file (~2900 lines), inline CSS + JS
- Firebase Realtime Database (REST API, no SDK)
- No build tools, no frameworks, no dependencies
- GitHub Pages for hosting
