# lovelog-site

Marketing and legal site for LoveLog, plain static HTML, no build step. Deployed on Vercel
(project `lovelog-legal`, linked to `hgrdic/lovelog-site`), custom domain `lovelog.back-slash.hr`,
matching how `nula.back-slash.hr` is deployed.

## Pages
- `/` and `/hr/` — home (English default, Croatian toggle)
- `/privacy` and `/hr/privacy` — Privacy Policy
- `/terms` and `/hr/terms` — Terms of Use
- `/support` and `/hr/support` — Support

## Deploy
The Vercel project `lovelog-legal` is already created and linked to this repo, so every push to
`main` deploys to production automatically. Framework preset is "Other" (static HTML, no build
command). Vercel serves clean URLs, so `/privacy` resolves to `privacy/index.html`.

Custom domain (one-time, done in the Vercel dashboard, not via API):
1. Vercel, then the `lovelog-legal` project, then Settings, then Domains. Add
   `lovelog.back-slash.hr`. If that subdomain is attached to another Vercel project, remove it
   there first, a domain points at one project at a time.
2. In Cloudflare DNS for `back-slash.hr`, add a CNAME record: `lovelog` to `cname.vercel-dns.com`,
   DNS only (grey cloud, not proxied), matching the `nula` record.
3. Vercel issues the TLS certificate automatically once DNS resolves (a few minutes, up to a few
   hours).

The `CNAME` file in the repo root is a leftover from the GitHub Pages plan and is ignored by
Vercel. It can stay or be deleted.

## To do around submission
- **App Store link.** The App Store Connect app ID is `6803402403`, so the store URL will be
  `https://apps.apple.com/app/id6803402403` once the app is public. There is no "Get the app"
  button yet because the app is still in review, matching brojko's approach. Add the button to the
  nav and hero (English and Croatian) when the listing goes live.
- **App Store Connect metadata.** Point the listing's Privacy Policy URL at
  `https://lovelog.back-slash.hr/privacy` and the Support URL at
  `https://lovelog.back-slash.hr/support`. Both fields are editable while the build is
  "Waiting for Review".
- **In-app legal links.** `apps/lovelog` currently opens `/privacy` and `/terms` on
  `WEB_BASE_URL` (`cheese-app-eta.vercel.app`) from `account.tsx` and `upgrade.tsx`. Do **not**
  repoint `WEB_BASE_URL` itself, it also builds the pairing `inviteLink()` and the
  `INVITE_EMAIL_ENDPOINT`, both served by the Vercel app. To move the legal links here, add a
  separate `LEGAL_BASE_URL` constant in `apps/lovelog/lib/config.ts` and use it at those call
  sites. Build 32 is already in review, so the shipped binary keeps the Vercel links regardless.
- Check the "Last updated" dates on the privacy and terms pages before the first submission, and
  again any time the app's data practices actually change.

## Editing legal copy
The Croatian pages under `/hr/` mirror the English ones. When you change a sub-processor, a
retention period, or any other substantive point in an English legal page, make the matching
change in its `/hr/` counterpart in the same commit, or the two will drift.

## Content notes
- Backend is Cloudflare (D1, R2, Workers, Better Auth), EU region. Sign-in is magic link or
  Google/Apple, no password. Email is Resend. Subscriptions are Apple plus RevenueCat. Analytics
  is PostHog's EU instance, anonymised; there is no in-app analytics opt-out, so the pages don't
  claim one. Sentry is wired in the app but has no DSN set in any build profile, so it is not
  listed as a sub-processor.
- Letters are encrypted at rest with a server-issued key, not end-to-end, so the pages say
  "encrypted" and "sealed until the reveal date", not "end-to-end encrypted".
