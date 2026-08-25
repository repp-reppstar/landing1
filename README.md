# Reppstar Technologies — landing page

Static landing page (`index.html`) + one Vercel serverless function
(`api/notify.js`) that sends a confirmation email through **Resend** when
someone submits the waitlist form.

## 1. Get a Resend API key

1. Sign up / log in at https://resend.com
2. Dashboard → **API Keys** → Create API Key (Sending access is enough)
3. Copy the key (starts with `re_`) — you won't be able to see it again

## 2. Local development

```bash
npm install
cp .env.local.example .env.local
```

Paste your real key into `.env.local`:

```
RESEND_API_KEY=re_your_real_key
```

Run it locally with the Vercel CLI (installs on first use):

```bash
npx vercel dev
```

This serves `index.html` and `api/notify.js` together on one local port so
the fetch call to `/api/notify` works exactly like it will in production.

## 3. Deploy to Vercel

```bash
npx vercel        # first deploy, follow the prompts
npx vercel --prod # promote to production
```

Then add the same environment variable in the Vercel dashboard (**Project →
Settings → Environment Variables**), or via CLI:

```bash
npx vercel env add RESEND_API_KEY
```

Redeploy after adding it (`npx vercel --prod`) so the function picks it up.

## 4. Important: sender domain

By default `api/notify.js` sends from Resend's shared address
`onboarding@resend.dev`. Resend restricts that address so it can **only
deliver to the email you signed up to Resend with** — good enough to test
with your own inbox, but it will silently fail for real visitors.

To send to anyone:

1. Resend dashboard → **Domains** → Add your domain (e.g. `reppstar.com`)
2. Add the DNS records Resend gives you (SPF/DKIM) at your DNS provider
3. Once verified, set `RESEND_FROM` (see `.env.local.example`) to an address
   on that domain, e.g. `Reppstar Technologies <hello@reppstar.com>`

## Files

- `index.html` — the page. The waitlist form POSTs JSON to `/api/notify`
  and shows the result inline (no page reload).
- `api/notify.js` — validates the email, calls `resend.emails.send(...)`,
  returns JSON. The API key never touches the browser.
- `repp-logo.png` / `repp-logo-web.png` — logo assets referenced by the page.
