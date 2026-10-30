# Travel with Prem

Website for Travel with Prem: travel agency and travel guide store.

Built with [Astro](https://astro.build) and deployed to [Cloudflare Workers](https://developers.cloudflare.com/workers/) via the `@astrojs/cloudflare` adapter.

## Requirements

- Node.js 22.23.4 or newer (`.node-version` pins 24 for Cloudflare builds)

## Commands

| Command                  | Action                                           |
| :----------------------- | :----------------------------------------------- |
| `npm install`            | Install dependencies                             |
| `npm run dev`            | Start the dev server at `localhost:4321`         |
| `npm run build`          | Build the site to `./dist/`                      |
| `npm run preview`        | Preview the build locally                        |
| `npm run generate-types` | Generate types for Cloudflare bindings           |
| `npx wrangler deploy`    | Deploy to Cloudflare (after `npm run build`)     |

## Deploying to Cloudflare

Connect this repository in the Cloudflare dashboard (Workers & Pages → Create → Import a repository) with:

- Build command: `npm run build`
- Deploy command: `npx wrangler deploy`

Configuration lives in `wrangler.jsonc`.
