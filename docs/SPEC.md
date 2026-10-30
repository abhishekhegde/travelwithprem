# Travel With Prem: Product Spec

Working spec for building the site. Keep it current as decisions get made.

**Status:** Spec drafted. Waiting on design inspiration from the owner before starting UI work.

---

## 1. What we're building

A personal-brand travel business for **Prem Sarangi** ("Travel With Prem", tagline *Explore • Experience • Remember*). The site is about Prem, not a faceless agency: his photos, his routes, his voice.

The site has two ways to work with Prem:

1. **Plan with Prem.** A visitor talks to Prem, who plans the trip for them. This is a high-touch, higher-value service, and the site's job is to capture a qualified enquiry.
2. **Itinerary catalogue.** A visitor buys one of Prem's ready-made itineraries. It's a low-price digital product, gated behind payment, that comes as:
   - a **beautiful web version** (the main reading experience, mobile-first), and
   - a **downloadable PDF** (offline and on the trip).

The two journeys feed each other:
- An itinerary buyer is the warmest possible lead for planning. Every itinerary (preview, reader, PDF, emails) carries a **"Want Prem to tailor this trip for you?"** upsell.
- A planning enquiry for a destination with an itinerary can point the person to it.

### Positioning
- **Who it's for:** Indian travellers (mostly on mobile) heading to the Himalayas, on pilgrimages, or to Nepal and Bhutan. Prices are in INR.
- **Why Prem:** information that's been verified first-hand, practical day-wise plans with maps, real photography, secure payment (UPI and cards), and instant delivery.

---

## 2. Brand & design (from the owner's mockup; to be refined with inspiration)

| Element | Observed in mockup |
| --- | --- |
| Logo | Line-art mountains + camera + hiker, brush-script "Travel With **Prem**" ("Prem" in gold), small caps "EXPLORE • EXPERIENCE • REMEMBER" |
| Palette | Forest green (primary buttons) ~`#1F5A45`, deep navy (footer and dark bands) ~`#13202D`, warm gold accent ~`#F5B935`, cream page background ~`#F7F3EC`, near-black ink |
| Type | Brush script for the logo and accents ("Good Vibes Only"); bold serif for section headings; clean sans for body; letter-spaced uppercase eyebrows ("FEATURED TRAVEL GUIDES") |
| Imagery | Full-bleed golden-hour Himalayan hero with a traveller; product cards with a photo, a "Digital Download" badge and the price |
| Iconography | Thin line icons in navy/green (categories, the "Why" band) |

**Principles**
- Use **Prem's own photography only** in the end product. Mockup or stock images are placeholders. Authenticity is the brand.
- Design mobile-first. The reader must be a pleasure on a phone.
- Make it feel like a travel journal or magazine, not an e-commerce template.

**Mockup homepage, top to bottom:** nav (Home, Destinations ▾, Travel Guides ▾, About, Contact, plus search, account and cart) → hero with search → category strip (Himalayas, Pilgrimage, Nature, Photography, Adventure, Cultural) → "Plan Your Next Journey" featured guides (3 cards) → "Why Travel With Prem" band (Verified Information, Practical Itineraries, Stunning Photography, Secure Payments, Instant Delivery) → footer (quick links, popular destinations, socials, newsletter, legal links).

> The mockup has no "Plan with Prem" entry point. Add one to the nav and give it a dedicated home section; it's half the business.

---

## 3. User journeys

### J1: Plan with Prem
1. The visitor lands on Home or an itinerary page, then clicks "Plan my trip with Prem".
2. They open `/plan` and see how it works (3 steps), what's included, past trips and testimonials, and pricing or "starts from" (TBD).
3. They fill in a short enquiry form: name, email, WhatsApp number, destination(s), rough dates or month, number of days, group size and type (solo, couple, family, friends), budget band, travel style, notes. The form is protected by Turnstile.
4. On submit:
   - the lead is stored in D1;
   - Prem gets an email (and WhatsApp later, optionally);
   - the customer gets an auto-reply.
5. The thank-you page shows the next steps, a "Chat on WhatsApp" button (`wa.me` deep link with a prefilled message), and relevant itineraries.
6. *(Option, TBD)* A paid consultation call (fixed fee via Razorpay plus a booking link) instead of a free enquiry.

### J2: Buy an itinerary
1. The visitor browses `/itineraries` (filter by destination, category, days) or arrives from search or social.
2. The public itinerary page at `/itineraries/[slug]` is a rich preview: hero, summary, highlights, route map, at-a-glance facts (days, best season, difficulty, budget band), **free sample of Day 1** with the rest blurred or locked, a "What's inside" list, sample PDF pages, FAQs, price and a **Buy** button.
3. **Checkout:** collect email and phone, then open Razorpay Checkout (UPI, GPay, PhonePe, cards).
4. **On payment success:**
   - the server verifies the signature;
   - it records the order and grants the entitlement;
   - it sets the access session;
   - it redirects to the reader.
5. A **receipt email** goes out with the reader link, the PDF download link, the invoice and the "Plan with Prem" upsell.
6. **Reader** at `/itineraries/[slug]/read` is the full gated web version: day-by-day sections, a sticky day navigator, maps, stays, costs, tips, packing list, photos and a "Download PDF" button.
7. **Returning buyer:** goes to `/account` (My Guides), enters their email, gets a magic link, then sees all purchased guides. No passwords.

### Upsell touchpoints
- The itinerary preview page has a secondary CTA, "Rather have Prem plan it for you?"
- The reader has an inline card mid-itinerary and at the end: "Customise this route with Prem".
- The PDF has a last page with a QR code or link to `/plan?from=<slug>`.
- The receipt email and a follow-up email a few days later (optional) point to planning.
- Pass `from=<slug>` through to the lead so Prem knows the context.

---

## 4. Sitemap & routes

| Route | Render | Purpose |
| --- | --- | --- |
| `/` | static | Home |
| `/plan` | static + action | Plan with Prem landing + enquiry form |
| `/plan/thanks` | static | Enquiry confirmation |
| `/itineraries` | static | Catalogue with filters |
| `/itineraries/[slug]` | static | Public preview / product page |
| `/itineraries/[slug]/read` | **server** | Gated web reader (entitlement check) |
| `/destinations`, `/destinations/[slug]` | static | Destination hubs (Nepal, Bhutan, India, Himalayas...) linking itineraries and planning |
| `/about` | static | Prem's story, photos, credibility |
| `/contact` | static | Contact details + form + WhatsApp |
| `/checkout/success` | server | Post-payment landing → reader |
| `/account` | server | Magic-link sign-in + My Guides |
| `/api/checkout/order` | server | Create Razorpay order |
| `/api/checkout/verify` | server | Verify payment signature, grant access |
| `/api/webhooks/razorpay` | server | Webhook (source of truth, idempotent) |
| `/api/download/[slug]` | server | Entitlement-checked PDF stream from R2 |
| `/api/auth/magic` | server | Issue / consume magic-link tokens |
| `/print/[slug]` | server, secret-protected | Print-styled itinerary used to generate PDFs |
| `/privacy`, `/terms`, `/refund-policy`, `/delivery-policy` | static | Legal (also needed for Razorpay activation) |

Public pages are prerendered (fast, SEO). Gated and transactional routes use `export const prerender = false`.

---

## 5. Content model

All itinerary content lives in the repo as an Astro content collection: **one source** renders the preview, the reader and the PDF.

`src/content/itineraries/<slug>/index.mdx` (+ images alongside)

```yaml
slug: mustang-muktinath
title: Mustang to Muktinath Travel Guide
subtitle: "..."
destination: nepal            # ref → destinations collection
categories: [himalayas, pilgrimage, adventure]
days: 8
type: Detailed Itinerary      # card label
price: 399                    # INR, server-side source of truth
compareAtPrice: null
status: published             # draft | published
cover: ./cover.jpg
gallery: [...]
summary: "Route, itinerary, accommodation areas and travel tips."
highlights: [...]
bestSeason: "Apr–Jun, Sep–Nov"
difficulty: moderate          # easy | moderate | challenging
budget: { low: 25000, high: 45000, note: "per person, ex-travel to Kathmandu" }
startEnd: { start: Kathmandu, end: Pokhara }
mapImage / route geojson: ...
previewDays: [1]              # days shown free on the public page
faqs: [...]
updatedAt: 2026-10-01         # "Verified" trust signal
pdfVersion: 1                 # bump when PDF regenerates
```

The body is structured **day blocks** (MDX components): `<Day n title route distance altitude stay meals>` containing narrative, tips (`<Tip>`), costs (`<Cost>`), photos and an optional map. Separate sections cover the packing list, permits/documents, budget breakdown, contacts and "Prem's notes".

Other collections:
- `destinations`: name, country, hero, intro, best time.
- `categories`: the 6 from the mockup.
- `testimonials`.

**Gating rule:**
- Full day content is only rendered by server routes after the entitlement check, and the public preview page renders only `previewDays`.
- Never put full itinerary text in a prerendered page or a client bundle.
- Never put PDFs in `public/`.

Seed catalogue (prices illustrative, per owner):

| Itinerary | Country | Days | Label | Price |
| --- | --- | --- | --- | --- |
| Mustang to Muktinath Travel Guide | Nepal | 8 | Detailed Itinerary | ₹399 |
| Chardham Yatra Planner | India | 10 | Pilgrimage Guide | ₹299 |
| Bhutan Mountain Escape | Bhutan | 7 | Scenic Itinerary | ₹349 |

---

## 6. Architecture

Stay on **Astro 7 + Cloudflare**. The ChatGPT draft suggested Next.js + Supabase + Razorpay; everything that stack provides maps onto Cloudflare, so we use that instead:

| Need | Choice |
| --- | --- |
| Hosting / SSR | Cloudflare Workers via `@astrojs/cloudflare` (static assets + Worker for server routes) |
| Database (orders, entitlements, leads, tokens) | **D1** |
| PDF + private file storage | **R2** (private bucket, streamed through `/api/download`) |
| Sessions | Astro sessions on **KV** (`SESSION` binding, already enabled by the adapter) |
| Images | Astro `<Image>` + Cloudflare Images binding (`IMAGES`, already enabled) |
| Payments | **Razorpay** Standard Checkout (UPI, GPay, PhonePe, cards) |
| Transactional email | Resend (HTTP API from the Worker); evaluate Cloudflare Email Service as an alternative |
| Spam protection | Cloudflare Turnstile on forms |
| Forms / mutations | Astro Actions |
| Analytics | Cloudflare Web Analytics |
| Bindings in code | `import { env } from 'cloudflare:workers'` |

### Data (D1)
- `customers(id, email UNIQUE, phone, name, created_at)`
- `orders(id, customer_id, razorpay_order_id UNIQUE, razorpay_payment_id, status[created|paid|failed|refunded], amount_paise, currency, items_json, created_at, paid_at)`
- `entitlements(customer_id, itinerary_slug, order_id, granted_at, revoked_at, PRIMARY KEY(customer_id, itinerary_slug))`
- `leads(id, name, email, phone, destinations, dates, days, group, budget, style, notes, source_slug, status[new|contacted|won|lost], created_at)`
- `auth_tokens(token_hash, email, expires_at, used_at)`
- `newsletter(email, created_at)`

### Payment flow (must be done exactly)
1. `POST /api/checkout/order {slug, email, phone}`: the server looks up the **price from the content collection** (never trust the client), creates a Razorpay order (amount in paise, `receipt` = our order id, `notes` = slug + email), inserts `orders(status=created)` and returns `order_id` + key id.
2. The client opens Razorpay Checkout with `order_id` and prefilled email/phone.
3. `POST /api/checkout/verify {razorpay_order_id, razorpay_payment_id, razorpay_signature}`: the server checks `HMAC_SHA256(order_id + "|" + payment_id, KEY_SECRET) == signature` (constant-time). On success it marks the order paid, grants the entitlement, sets a session for that email and returns the reader URL.
4. `POST /api/webhooks/razorpay` verifies `X-Razorpay-Signature` (HMAC of the raw body with the webhook secret) and handles `payment.captured` / `order.paid` **idempotently**. It's the fallback if the user closes the tab before step 3. The receipt email is sent once, by whichever path first transitions the order to paid.
5. Secrets go in Worker secrets (`RAZORPAY_KEY_ID`, `RAZORPAY_KEY_SECRET`, `RAZORPAY_WEBHOOK_SECRET`, `RESEND_API_KEY`, `MAGIC_LINK_SECRET`, `PRINT_SECRET`) and `.dev.vars` locally. Use Razorpay **test mode** until launch.

### Access
- Session cookie (HttpOnly, Secure, SameSite=Lax) via Astro sessions, holding the customer id.
- The magic link is a random token whose hash is stored in D1, with a 20-minute expiry and single use. Consuming it creates the session.
- Reader and download routes check `entitlements` for (customer, slug). Without access, redirect to the product page with "Already bought? Get your link".

### PDF pipeline
- `/print/[slug]` renders the full itinerary with a print stylesheet: A4, cover page, contents, day pages, maps, packing list, and a closing "Plan with Prem" page with a QR code. It's protected by `PRINT_SECRET`.
- `npm run pdf` uses Playwright (`page.pdf()`) against the local preview, writes `pdf/<slug>-v<pdfVersion>.pdf`, then uploads to R2 with `wrangler r2 object put`. Later this moves into CI.
- `/api/download/[slug]` streams from R2 with `Content-Disposition: attachment` after the entitlement check.
- *(v2)* Stamp "Licensed to <email>" on each page at download time with `pdf-lib` in the Worker to discourage sharing.

### Emails
- Purchase receipt: reader link, PDF link, invoice details, upsell.
- Magic link.
- Planning enquiry, sent to Prem (with all fields and the source slug) and as an auto-reply to the customer.
- *(Optional)* A follow-up a few days after purchase: "How's the planning going? Prem can tailor it."

---

## 7. SEO, performance, quality
- Prerender every public page; target Lighthouse 95+ on mobile; responsive images (AVIF/WebP).
- Structured data: `Person` (Prem), `Product` + `Offer` (itineraries, INR), `TouristTrip`, `BreadcrumbList`, `FAQPage`.
- `@astrojs/sitemap`, canonical URLs, Open Graph images per itinerary.
- Free preview content (Day 1, highlights, FAQs) is indexable, which helps rank for "Mustang Muktinath itinerary"-type searches.
- Accessibility: semantic headings, focus states, sufficient contrast on photo overlays, alt text on all photos.

## 8. Legal & business prerequisites
Razorpay activation typically requires these to be live on the site: **Contact Us, Terms & Conditions, Privacy Policy, Cancellation & Refund Policy, Shipping/Delivery Policy** (for digital goods: "delivered instantly by email and web access"), plus visible product prices. Also decide:
- the refund policy for digital goods;
- GST registration and invoices;
- the business name shown on receipts.

---

## 9. Milestones
- **M0: Scaffold** ✅ Astro 7 + Cloudflare adapter, pushed to `main`.
- **M1: Design system + public site.** Tokens, typography, logo, layout, nav/footer; Home, Itineraries list, Itinerary preview, Plan with Prem, About, Contact, Destinations, legal pages. Uses the 3 seed itineraries with placeholder content. Deploy a preview to Cloudflare.
- **M2: Itinerary content + reader + PDF.** Full content model and MDX components; gated reader UI (temporarily open in dev); print route; PDF generation script.
- **M3: Commerce.** D1 schema and migrations; Razorpay test-mode checkout, verify and webhook; entitlements; sessions; magic link; R2 downloads; receipt email.
- **M4: Plan with Prem backend.** Leads in D1, Turnstile, emails to Prem and the customer, WhatsApp CTA; a simple way for Prem to see leads (email first; maybe a protected `/admin/leads` later).
- **M5: Launch.** Domain on Cloudflare, Razorpay live keys, analytics, final content and photos, legal pages reviewed.

## 10. Open questions for Prem
1. **Domain:** `premsarangi.com` (seen in the draft) or a `travelwithprem` domain, or both with a redirect?
2. **Plan with Prem:** a free enquiry, or a paid consultation (fee, call length)? Should the itinerary price be credited if they book planning?
3. **Cart vs Buy now:** with a handful of guides, a single-item "Buy now" is simpler and converts better. Keep the cart icon from the mockup for bundles later?
4. **Bundles:** for example a "Himalaya pack" at a discount?
5. **PDF delivery:** a download link in the email plus "My Guides" (recommended; links can be re-sent, files stay private), or a PDF attachment too?
6. **Free sample:** Day 1 only, or Day 1 plus a sample PDF?
7. **Updates:** do buyers get free updated versions of a guide (the "Verified Information" promise)?
8. **Language:** English only at launch, or Hindi too?
9. **Real content:** Prem's photos, bio, socials (YouTube, Instagram, Facebook, X, Pinterest handles), WhatsApp number, testimonials.
10. **Email sending domain / from-address** for receipts.
11. **Newsletter:** collect in D1 only, or connect a provider?
