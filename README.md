# 📚 AI Reading Recommender

A fully client-side tool that builds a personalized, up-to-date reading list on any topic — Gemini searches the live web and returns real, clickable sources. No backend, no build step, just one HTML file.

## What it does

1. You type what you're interested in, in plain words (e.g. *"AI agents, startup funding, UX design trends"*)
2. The page sends your interests to the **Gemini API** with **Grounding with Google Search** enabled, so the model searches the live web instead of relying only on its training data
3. Gemini returns 5-6 recent, relevant items, each with a short title, why it's worth reading, and the source name
4. The real source links the model actually used are listed underneath, taken from the API's grounding metadata — not generated text, so they point to pages that exist

## ⚠️ API key requirement — please read

This tool relies on Gemini's **Grounding with Google Search** feature. **Not every Gemini API key has quota for it.** On a free key without billing enabled, grounding quota may be zero, in which case the page will show an error like:

> Quota or rate limit reached. Details: You exceeded your current quota, please check your plan and billing details.

This is not a bug in the page — it means your key isn't allowed to use web search grounding. To use the tool you need a key from a Google AI Studio project where grounding is available, which typically means **billing is enabled** on that project. You can check your current quotas at [ai.dev/rate-limit](https://ai.dev/rate-limit), and Google's pricing and limits are documented at [ai.google.dev/gemini-api/docs/rate-limits](https://ai.google.dev/gemini-api/docs/rate-limits).

## Bring your own API key — and why

The page asks each visitor for their own Gemini API key instead of shipping one. That's a deliberate security choice:

- The page is 100% static, served by GitHub Pages. There is no server anywhere to keep a secret hidden.
- A key hardcoded into the page's JavaScript could be read by anyone through their browser's developer tools.
- Instead, each visitor's key is used only to call Gemini directly from their own browser. It is never sent anywhere else, and it is only saved (in that browser's local storage) if the visitor checks "Remember this key in this browser only".

## How it works

- **Language:** HTML, CSS, and vanilla JavaScript — no framework, no build step
- **AI:** [Google Gemini API](https://ai.google.dev/) (`gemini-3.5-flash-lite`) with the `google_search` grounding tool
- **Hosting:** [GitHub Pages](https://pages.github.com/)
- **No server, no database, no GitHub Actions, no repository secrets**

## Architecture

```
index.html   → The entire application: markup, styling, and the Gemini API call
```

## Setup

1. Fork or clone this repository
2. Enable GitHub Pages: **Settings → Pages → Source: Deploy from a branch → Branch: main, / (root)**
3. Open the published page, paste in a Gemini API key that has grounding quota (see the requirement above), type your interests, and click **Get My Reading List**

## Customization

- **Change the model:** edit the model name in the `url` constant inside `getRecommendations()`. Model availability changes over time; to see which models your key supports, open `https://generativelanguage.googleapis.com/v1beta/models?key=YOUR_KEY` in a browser.
- **Change the output:** edit the `prompt` string inside `getRecommendations()` to change how many items are returned, their format, or the tone.

## Robustness notes

- **Errors are never silent.** Any API error is shown on the page together with Google's own error message, and any unexpected script error is caught and displayed as well — so the cause of a failure is always visible without opening developer tools.
- **Blocked browser storage doesn't break the page.** Strict privacy settings can make `localStorage` throw an error. All storage access is wrapped so that, in that case, only the "remember my key" convenience is lost and the tool keeps working.
- **Script-blocking extensions:** browser extensions that block JavaScript (for example NoScript or strict content blockers) can stop the page from running at all, with no visible error. If the button does nothing, try another browser or temporarily disable such extensions for this page.# ai-reading-recommender
