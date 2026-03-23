# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-page web app for running an AI App Challenge workshop at The Walt Disney Company Italy. Teams submit AI app briefs, which are scored by both a human judge and an AI co-judge (Claude), with a live leaderboard. The UI language is Italian.

## Architecture

- **`index.html`** — The entire frontend: HTML, CSS, and JS in one file. Uses Firebase Realtime Database (via CDN SDK) for live data sync and a Netlify serverless function as an API proxy.
- **`netlify/functions/judge.js`** — Netlify Function (v1, CommonJS) that proxies requests to the Anthropic Messages API. Reads `ANTHROPIC_API_KEY` from environment. Called from the frontend at `/api/judge`.
- **`netlify.toml`** — Netlify config: publishes root directory, sets functions directory, and redirects `/api/*` to `/.netlify/functions/:splat`.

## Key Data Flow

1. Teams submit briefs via the "Invia" tab → written to Firebase at `submissions/{teamKey}`
2. Human judge scores locally in the "Giudica" tab (scores kept in-memory, not persisted to Firebase)
3. AI co-judge button calls `/api/judge` → Netlify function → Anthropic API (`claude-sonnet-4-20250514`) → LLM scores saved to Firebase at `scores/{teamKey}`
4. Leaderboard ("Classifica" tab) ranks teams by average of human + AI scores, updated in real-time

## Development

- **No build step** — static HTML served directly. No package.json, no bundler.
- **Local dev**: Use `netlify dev` to run locally (serves static files + functions with the `/api/*` redirect).
- **Environment**: Set `ANTHROPIC_API_KEY` in Netlify dashboard or `.env` file for local dev.
- **Deployment**: Hosted on Netlify. Push to `main` triggers deploy.

## Firebase

- Project ID: `disney-worskhop` (note: intentional typo in the project name)
- Uses Realtime Database (europe-west1), not Firestore
- No authentication — open read/write for workshop simplicity
