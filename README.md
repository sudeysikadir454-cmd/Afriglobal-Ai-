# AfriGlobal AI V16.1 — Complete Deploy Build

**AfriGlobal AI — Built in Africa, Created for the World.**

This build combines the requested mobile chat experience, real authentication, cloud chat history, files/image understanding, search, voice, safety, and PWA support.

## Included

- AfriGlobal AI branded Chat UI
- Real email/password Sign up and Log in
- Account profile created automatically after signup
- Supabase Postgres cloud conversations and messages
- Local history plus cloud history when signed in
- New Chat, chat search, Projects, Apps, Recents/History
- Multilingual Auto Detect
- Web Search toggle
- Think harder toggle
- Image understanding
- PDF / CSV / TXT analysis
- Camera / Photos / Files attachment flow
- Image preview before sending
- Send button and microphone input
- Real-time voice mode through a secure server session
- Voice transcription and audio response
- Copy / Like / Dislike / Share / Read Aloud
- User message menu: Copy, Select text, Edit message, Share prompt
- Service status check with a clickable `/api/health` link
- AI Safety Center
- PWA install support
- Server-only AI secret
- No provider/model/secret details shown in the normal UI

## 1. Supabase setup

1. Create/open your Supabase project.
2. Open **SQL Editor → New Query**.
3. Run `supabase-schema.sql`.
4. In **Authentication → Sign In / Providers**, keep Email enabled.
5. In **Authentication → URL Configuration**, set the Site URL to your Vercel app URL and add:

```text
https://YOUR-VERCEL-DOMAIN.vercel.app/**
```

The schema creates:

- `profiles`
- `conversations`
- `messages`

Row Level Security limits each signed-in user to their own data.

### Optional: Google sign-in (OAuth)

The V17.1 interface includes **Continue with Google**. For the button to complete sign-in, enable Google as an OAuth provider in Supabase:

1. In Google Cloud Console, create an OAuth client for a Web application and configure the authorized redirect URI shown by Supabase under **Authentication → Sign In / Providers → Google** (usually `https://YOUR_PROJECT.supabase.co/auth/v1/callback`).
2. Copy the Google OAuth Client ID and Client Secret into the Supabase Google provider settings. Keep the client secret private.
3. In Supabase **Authentication → URL Configuration**, set your production Site URL and allow-list `https://YOUR-VERCEL-DOMAIN.vercel.app/**` as a redirect URL.
4. Redeploy the app if you changed Vercel environment variables. Then test Google sign-in on the deployed HTTPS site.

If Google provider is not enabled in Supabase, the button cannot sign the user in; configure the provider first.

## 2. Vercel environment variables

Add these in **Project → Settings → Environment Variables**:

```text
AI_PROVIDER=auto
GROQ_API_KEY=YOUR_GROQ_API_KEY
GROQ_MODEL=llama-3.3-70b-versatile
GEMINI_API_KEY=YOUR_GEMINI_API_KEY
GEMINI_MODEL=gemini-3.5-flash-lite
GEMINI_LIVE_MODEL=gemini-2.5-flash-native-audio-preview-12-2025
NEXT_PUBLIC_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=YOUR_SUPABASE_PUBLISHABLE_OR_ANON_KEY
```

The public Supabase URL/key are intended for the browser. The AI secret must stay server-side and must never use a `NEXT_PUBLIC_` prefix.

After changing environment variables, redeploy.

## 3. API endpoints

- `/api/chat` — text, search, thinking, image/PDF/text analysis
- `/api/live/token` — short-lived voice session token
- `/api/health` — public service health/status endpoint
- `/api/safety` — safety status endpoint

The Settings panel uses `/api/health` and provides an **Open service status** link.

## 4. Local development

```bash
npm install
npm run dev
```

Then open `http://localhost:3000`.

## Important

The ZIP is source/deployment ready, but a full local `next build` could not be completed in this environment because package installation timed out. The project files and route syntax were checked, but Vercel should perform the final dependency install/build during deployment.


## V16.4 — Secure Auth + Link Understanding

- Supabase Sign Up, Login, Logout, persisted sessions, profile creation trigger, and account-scoped cloud history.
- The Supabase client accepts `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` (recommended) and the older `NEXT_PUBLIC_SUPABASE_ANON_KEY` variable for backwards compatibility.
- When a user pastes a public `http://` or `https://` link into chat, the server automatically enables Gemini URL Context to inspect that page and explain its accessible content. Google Search remains an optional toggle.
- Image, PDF, CSV, and TXT attachments are processed by the server-side chat route.
- Gemini API key stays server-only as `GEMINI_API_KEY`; it must never be prefixed with `NEXT_PUBLIC_`.
- Browser source code cannot be made completely invisible in a public website. This project keeps secrets and Gemini calls on the server, and the database must use Row Level Security so each signed-in user can only access their own profile and chats.

### Vercel Environment Variables

Required for AI: `GEMINI_API_KEY`.

Required for Supabase auth: `NEXT_PUBLIC_SUPABASE_URL` and either `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` (recommended) or the existing `NEXT_PUBLIC_SUPABASE_ANON_KEY`. Set the values for Production, Preview, and Development as needed, then redeploy.

### Supabase setup

Run `supabase-schema.sql` in the Supabase SQL Editor if the profile, conversations, and messages tables/policies have not already been created. In Authentication → URL Configuration, set Site URL to the production HTTPS URL and add that URL plus `/**` to Redirect URLs. Keep RLS enabled on exposed tables. Never use `sb_secret_...` or `service_role` as a browser key.

### Link analysis limits

URL Context can inspect supported public pages. It cannot bypass logins, paywalls, private cloud drives, or site access controls, and it cannot guarantee that every page element or linked file is readable. If it cannot access a URL, AfriGlobal AI should say so instead of guessing.

## V17 update notes
- Preserves the V16.4 chat, Supabase auth/history, Gemini server route, attachment handling, URL-context support, and voice interface.
- Adds baseline HTTP security headers in `next.config.mjs` and removes the `X-Powered-By` response header.
- Adds `SECURITY-CHECKLIST.md` with deployment and verification steps.
- Voice recognition and read-aloud depend on browser/device support and microphone permissions. A visible control is not proof of live audio support on every device.
- This package has not been verified with a full production `next build` in this environment. Run `npm install` and `npm run build` before deploying, and fix any build errors shown by Vercel.


## V19.1 Image provider fallback
Optional image variables: `POLLINATIONS_API_KEY`, `POLLINATIONS_IMAGE_MODEL=flux`, `HF_TOKEN`, and `HF_IMAGE_MODEL`. Configure both providers for fallback: text-to-image tries Pollinations then Hugging Face; image editing uses Hugging Face. Current Pollinations API documentation requires a key, and free availability/credits are not guaranteed. See `V19-CREATE-IMAGE-FIX-OM.md`.

## V22.1 — Competition demo
See `V22.1-COMPETITION-DEMO-OM.md` for an Afaan Oromoo demo script, deployment checks, provider requirements and a practical judging checklist. Image generation/editing requires an enabled external provider and available quota; test these flows on a Vercel Preview deployment before presenting.

## V23 African Innovation Competition Edition
See `V23-COMPETITION-EDITION-OM.md` for the updated competition demo and provider checklist. Run `npm run preflight` to check essential project files and dependencies before the full build. This preflight does not test live API credentials, provider quota, or production deployment.
