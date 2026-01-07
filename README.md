# Astro Starter Kit: Blog

```sh
npm create astro@latest -- --template blog
```

> 🧑‍🚀 **Seasoned astronaut?** Delete this file. Have fun!

Features:

- ✅ Minimal styling (make it your own!)
- ✅ 100/100 Lighthouse performance
- ✅ SEO-friendly with canonical URLs and OpenGraph data
- ✅ Sitemap support
- ✅ RSS Feed support
- ✅ Markdown & MDX support

## 🚀 Project Structure

Inside of your Astro project, you'll see the following folders and files:

```text
├── public/
├── src/
│   ├── components/
│   ├── content/
│   ├── layouts/
│   └── pages/
├── astro.config.mjs
├── README.md
├── package.json
└── tsconfig.json
```

Astro looks for `.astro` or `.md` files in the `src/pages/` directory. Each page is exposed as a route based on its file name.

There's nothing special about `src/components/`, but that's where we like to put any Astro/React/Vue/Svelte/Preact components.

The `src/content/` directory contains "collections" of related Markdown and MDX documents. Use `getCollection()` to retrieve posts from `src/content/blog/`, and type-check your frontmatter using an optional schema. See [Astro's Content Collections docs](https://docs.astro.build/en/guides/content-collections/) to learn more.

Any static assets, like images, can be placed in the `public/` directory.

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `npm install`             | Installs dependencies                            |
| `npm run dev`             | Starts local dev server at `localhost:4321`      |
| `npm run build`           | Build your production site to `./dist/`          |
| `npm run preview`         | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |

## Decap CMS (Git-backed admin)

This repo is configured to use **Decap CMS** so you can edit blog posts with a friendly UI and commit changes back to GitHub.

- Admin UI: `https://<your-site>/admin/`
- Blog content folder: `src/content/blog/`
- Uploaded images:
  - stored in: `public/uploads/`
  - referenced in content as: `/uploads/<filename>`

### Using the editor
1. Deploy the site.
2. Open `/admin/`.
3. Log in (requires GitHub OAuth; see below).
4. Create/edit posts. Decap will commit Markdown files to `src/content/blog/`.

### Authentication (GitHub)
Decap's `github` backend requires an OAuth "auth server" that performs the GitHub OAuth flow.

You have two realistic options:

#### Option A (fastest): use a hosted OAuth proxy
Use any hosted Decap/Netlify CMS OAuth proxy service and set `backend.base_url` + `backend.auth_endpoint` accordingly.

Pros: fastest to get running.
Cons: relies on a third-party auth service.

#### Option B (recommended with Cloudflare Workers): host your own OAuth proxy
Host a tiny OAuth proxy (a separate Cloudflare Worker) that implements the GitHub OAuth flow, then point Decap to it.

At a high level:
1. Create a **GitHub OAuth App** (GitHub → Settings → Developer settings → OAuth Apps).
   - Authorization callback URL: `https://<your-auth-worker-domain>/callback`
2. Deploy an auth Worker that exposes endpoints like:
   - `/auth` (starts OAuth)
   - `/callback` (handles GitHub callback)
3. Update `public/admin/config.yml` to include:

```yaml
backend:
  name: github
  repo: pparulski/blog
  branch: main
  base_url: https://<your-auth-worker-domain>
  auth_endpoint: auth
```

If you want, I can implement the Cloudflare Worker OAuth proxy in this repo (or as a separate worker project) and tell you exactly which GitHub OAuth settings and secrets to set.

## 👀 Want to learn more?

Check out [our documentation](https://docs.astro.build) or jump into our [Discord server](https://astro.build/chat).

## Credit

This theme is based off of the lovely [Bear Blog](https://github.com/HermanMartinus/bearblog/).
