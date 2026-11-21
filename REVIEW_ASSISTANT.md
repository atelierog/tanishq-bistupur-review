# Tanishq Bistupur — Genuine Review Assistant

A mobile-first customer review-writing helper for the Diamond Expertise Centre.

## Customer flow
1. Customer writes a short account of what actually happened.
2. They choose English or Hinglish.
3. The app sends the text to the dedicated Cloudflare Workers AI endpoint for polishing.
4. If AI is unavailable or the daily free allocation is exhausted, local offline cleanup is used.
5. Customer edits or rejects the draft, copies it, and independently opens Google Reviews.

There are no seeded reviews, positive-only prompts, review gating, automatic submissions, tracking scripts, analytics, or application database for customer review text.

## AI service — connected
- Worker: `tanishq-qr-ai-review`
- Endpoint: `https://tanishq-qr-ai-review.atelierog-co.workers.dev/rewrite`
- Health: `https://tanishq-qr-ai-review.atelierog-co.workers.dev/health`
- Model: `@cf/qwen/qwen3.8-27b`
- Runtime: Cloudflare Worker with a Workers AI binding named `AI`
- CORS: restricted to `https://atelierog.github.io`
- Input limit: 1,200 characters
- Basic per-IP rate guard: 12 requests per minute per Worker isolate
- AI thinking disabled for concise, direct copy editing
- Worker does not log or persist customer review text. The selected model is marked zero-data-retention in Cloudflare's model catalog.

The endpoint accepts `POST /rewrite` with JSON `{ "text": "...", "language": "en" | "hinglish" }` and returns `{ "text": "...", "model": "..." }`.

## Cost guardrail
Cloudflare Workers AI currently provides 10,000 Neurons per day at no charge on Workers Free. The daily allowance is account-wide and resets at 00:00 UTC. Once the free limit is reached, inference requests fail; the frontend falls back to local cleanup. No paid Workers plan, paid API, prepaid credits, or paid service was enabled. Check current Cloudflare pricing and account usage because quotas and model prices can change.

## Offline fallback
Offline cleanup normalizes whitespace, sentence capitalization and punctuation. It does not perform semantic rewriting or translation. It remains available if the AI service is unavailable, rate-limited, or out of daily free capacity.

## Privacy and security
- Review text is sent to Cloudflare Workers AI only when the customer chooses Improve.
- The app has no review database, tracking scripts, analytics, or customer-text persistence.
- The Worker applies an origin allowlist, input length checks, and a best-effort in-memory per-IP rate guard. The rate guard is isolate-local, not a global quota.
- Avoid entering personal or sensitive information.
- Never put provider credentials in browser JavaScript.

## Manual smoke test
- Open the page from the GitHub Pages origin on a phone and desktop-sized browser.
- Test English and Hinglish output with short and detailed source text.
- Try positive, neutral, and negative experiences; verify the original sentiment is retained and no facts are invented.
- Try prompt-injection text in the input; it must be treated as source text, not instructions.
- Edit the draft, copy it, and verify the copied text.
- Confirm Google Reviews opens only after the customer taps the link.
- Confirm AI failures and rate limits fall back to offline cleanup.

## Policy and trust
The assistant is a writing aid, not a review generator or sentiment filter. Every customer should be given the same opportunity to leave honest feedback. Customers remain in control of the final text and submit it themselves. Follow Google's current Maps user-contributed content and Business Profile review policies.
