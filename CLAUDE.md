# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What Is Pangea

Pangea is a personal knowledge management web app. Users capture notes, upload PDFs/images/URLs, and get AI-generated summaries. Content is organized into 6 thematic "Panels." It also tracks a reading queue and a books list.

## Architecture

**No build step.** The entire frontend is a single HTML file (`pangea-v3/index.html`) that loads React 18, ReactDOM, Babel, and Supabase via CDN. JSX is transpiled in-browser by Babel. There is no npm, no bundler, no node_modules.

**Stack:**
- Frontend: React 18 (UMD/CDN) with in-browser Babel transpilation
- Database: Supabase (PostgreSQL) — credentials are hardcoded as publishable keys at the top of `index.html`
- AI: Claude API (`claude-sonnet-4-20250514`) accessed via a Netlify serverless proxy
- Hosting/Functions: Netlify (`netlify.toml` in `pangea-v3/`)

**Key files:**
- `pangea-v3/index.html` — entire frontend (~1000 lines, single file)
- `pangea-v3/functions/claude.js` — Netlify Function proxy for Claude API calls
- `pangea-v3/netlify.toml` — deploy config (publish dir is root, functions dir is `netlify/functions`)
- `index.html` (root) — legacy v2 using localStorage, not in active use

## The 6 Panels

Currents, Wharton, Tech & AI, Money, Creativity, The Soul — defined near the top of `index.html` as a constant array with name, icon, and color.

## Database Tables (Supabase)

- `entries` — journal entries (`id`, `date`, `time`, `type`, `text`, `panels[]`, `source_type`, `source_title`, `takeaway`)
- `queue` — reading list (`id`, `date`, `time`, `title`, `url`, `panel`, `done`)
- `books` — books list (`id`, `title`, `author`)

## Views / Navigation

Home → Journal → Queue → Panel detail. All routing is internal React state (no router library). Modals handle adding content (URL / PDF / Slides / Image tabs), AI analysis review, and filing queue items to journal.

## Development Workflow

Since there's no build step, editing `pangea-v3/index.html` directly and opening it in a browser is all that's needed for local development. For Netlify Functions, deploy to Netlify or use the Netlify CLI (`netlify dev`) to test the Claude proxy locally.

The Claude API key lives in Netlify's environment variables (not in source). The Supabase keys in source are publishable/anon keys — expected to be public.
