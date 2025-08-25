# RAG-frontend-project

A practical, no‑fluff guide to get this project running locally and in production.

---

## 1) Prerequisites

* **Node.js**: v18.18+ or v20+ (LTS recommended)
* **Package manager**: pick **one**: `npm` **or** `pnpm` **or** `yarn` **or** `bun`
* Optional: **Git**, **nvm** (to switch Node versions), **Docker**

> If you use `nvm`, run: `nvm install --lts && nvm use`.

---

## 2) First run (Local Development)

1. **Install deps** (choose one manager):

   * `npm install`
   * `pnpm install`
   * `yarn`
   * `bun install`
2. **Start dev server**:

   * `npm run dev`
   * `pnpm dev`
   * `yarn dev`
   * `bun dev`
3. Open: **[http://localhost:3000](http://localhost:3000)**

> Live reload (HMR) is enabled. Edit `app/page.tsx` and save to see updates.

---

## 3) Project Scripts

The exact set may vary, but these are standard:

```jsonc
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit"
  }
}
```

* **Dev**: runs the app with hot reload
* **Build**: creates an optimized production build in `.next`
* **Start**: serves the built app (use after `build`)
* **Lint**: runs ESLint with the Next.js config
* **type-check**: checks TypeScript types without emitting files

---

## 4) Environment Variables

Put local secrets in **`.env.local`** (git‑ignored by default). Example:

```bash
# .env.local
NEXT_PUBLIC_API_BASE_URL=http://localhost:4000
# Server-only vars (not exposed to the browser)
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
```

Usage in code:

* **Client**: only `NEXT_PUBLIC_*` is available
* **Server**: `process.env.YOUR_VAR`

Restart the dev server after adding new env vars.

---

## 5) Production (Local)

1. `npm run build`
2. `npm run start`
   The app starts on **[http://localhost:3000](http://localhost:3000)** (or the `PORT` you set).

Set `PORT=8080` (or another) to change the port.

---

## 6) Deployment Options

### a) Vercel (recommended)

* Create an account → New Project → Import your repo.
* Add **Environment Variables** in Vercel dashboard → Deploy.
* Vercel handles build & CDN automatically.

### b) Docker

**Dockerfile** (minimal):

```dockerfile
# ---- builder ----
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# ---- runner ----
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/package*.json ./
RUN npm ci --omit=dev
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/next.config.* ./
EXPOSE 3000
CMD ["npm", "run", "start"]
```

Build & run:

```bash
docker build -t my-next-app .
docker run -p 3000:3000 --env-file .env.local my-next-app
```

---

## 7) Fonts

This template uses [`next/font`](https://nextjs.org/docs/app/building-your-building/optimizing/fonts) and **Geist**. Nothing to configure unless you customize fonts. To change fonts, edit `app/layout.tsx`.

---

## 8) Troubleshooting

**A) Port already in use**

* Error: `Error: listen EADDRINUSE: address already in use 3000`
* Fix: kill the process or change port: `PORT=3001 npm run dev`

**B) Missing deps / lockfile conflicts**

* Delete lock & modules, then reinstall:

```bash
rm -rf node_modules package-lock.json pnpm-lock.yaml yarn.lock
npm install   # or your chosen manager
```

**C) Node version issues**

* Use LTS. With `nvm`: `nvm install --lts && nvm use`

**D) Env vars not picked up**

* Ensure `.env.local` exists at project root and restart dev server.

**E) TypeScript errors stop build**

* Run `npm run type-check` and fix the errors. Or temporarily set `"skipLibCheck": true` in `tsconfig.json` (not recommended long‑term).

**F) 404 on a new route**

* In the **App Router**, create a directory and `page.tsx`: `app/about/page.tsx`.

---

## 9) Editing Pages

* Home page: `app/page.tsx`
* Layout: `app/layout.tsx`
* New route: `app/<route>/page.tsx`
* API route: `app/api/<route>/route.ts`

---

## 10) Linting & Formatting

* ESLint: `npm run lint`
* Prettier (optional):

```bash
npm i -D prettier
npx prettier . --check
npx prettier . --write
```

---

## 11) Testing (optional)

Using **Vitest** + **Testing Library** (example):

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Add to `package.json`:

```jsonc
{
  "scripts": { "test": "vitest" }
}
```

Sample test file: `app/__tests__/page.test.tsx`

---

## 12) Project Structure (App Router)

```
app/
  layout.tsx       # wraps all pages
  page.tsx         # home page
  api/             # server routes
public/            # static assets
styles/            # global styles (if any)
```

---

## 13) Keep it Clean

* Use **one** package manager consistently (don’t mix `npm`, `pnpm`, `yarn`, `bun`).
* Commit your lockfile.
* Keep dependencies up to date.

---

**That’s it.** With the steps above you can install, run, build, and deploy this app rel
