# Tanishq Bistupur — Genuine Review Assistant

A mobile-first, static customer review-writing helper for the Diamond Expertise Centre.

## Customer flow
1. Customer writes a short account of what actually happened.
2. They choose English or Hinglish.
3. The app tries the configured AI endpoint when one exists; otherwise it uses a local, offline text cleanup.
4. Customer edits or rejects the draft, copies it, and independently opens Google Reviews.

There are no seeded reviews, positive-only prompts, review gating, automatic submissions, tracking scripts, analytics, or customer-text storage in this static page.

## Zero-cost behavior and current limitation
The offline fallback is free and works without a network request. It only normalizes whitespace, sentence capitalization and punctuation; it does **not** translate languages or perform semantic rewriting.

AI rewriting and English/Hinglish conversion require an inference endpoint. No provider credentials, paid API, or deployed AI service are included in this repository. To preserve zero-cost operation, connect only a provider/account with an available free allowance; free quotas and availability can change. If the endpoint is absent or fails, the app automatically falls back to local cleanup. Do not put provider secrets in browser JavaScript.

## Optional AI endpoint contract
Set `window.DXC_AI_ENDPOINT` to a same-origin HTTPS endpoint before the page script runs.

Request:
```json
{
  "text": "Customer's own description",
  "language": "en",
  "instruction": "Preserve all facts and sentiment; add no claims."
}
```

Response:
```json
{ "text": "Polished customer-authored review" }
```

The endpoint must:
- preserve positive, neutral, and negative sentiment equally;
- retain only facts supplied by the customer;
- never invent praise, staff names, products, services, outcomes, or claims;
- not filter, suppress, or discourage negative feedback;
- avoid logging or retaining review text;
- validate request size and allow only the intended site origin.

## Manual smoke test
- Open the page on a phone and desktop-sized browser.
- Submit an empty input: a clear validation message should appear.
- Try positive, neutral, and negative examples; offline cleanup must not add positive wording.
- Try English, Hindi, and Hinglish input; confirm original wording remains usable in offline mode.
- Edit the draft, copy it, and verify the copied text.
- Confirm Google Reviews opens only after the customer taps the link.
- Configure a test AI endpoint returning `{ "text": "..." }`; verify language selection is sent and endpoint failures fall back offline.

## Policy and trust
The assistant is a writing aid, not a review generator or sentiment filter. Every customer should be given the same opportunity to leave honest feedback. Customers remain in control of the final text and submit it themselves. Follow Google's current Maps user-contributed content and Business Profile review policies.
