# Travel With Prem

Read `docs/SPEC.md` before starting work. It is the product spec (journeys, routes, content model, payment flow, milestones, open questions). Update it when decisions change.

- Stack: Astro 7 + `@astrojs/cloudflare` on Cloudflare Workers; D1, R2, KV, Razorpay (see spec §6).
- Node ≥ 22.23.4 is required (`.node-version` pins 24). If the shell has an older Node: `source /opt/nvm/nvm.sh && nvm use 24`.
- Check changes with `npm run build`.
- Gated itinerary content must only render in server routes (`prerender = false`) after an entitlement check. Never put full itinerary text or PDFs in prerendered pages, client bundles or `public/`.
