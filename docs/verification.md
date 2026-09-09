# Verification record

Review performed September 5–9, 2026. Results concern the private implementation and explicitly named hosted observations; no Firebase deployment was performed.

## Available checks

| Check | Result | Scope and limitation |
| --- | --- | --- |
| Full local check: `npm run check` | Passed | Lint, 11 unit tests, frontend build, Functions build, and high-severity production dependency audit gates. |
| Firebase emulator suite | Passed: 6 tests | Firestore/Storage authorization plus server submission integration, using an isolated `demo-` project. |
| Current application lint | Passed: 0 errors, 0 warnings | Generated output and a stale nested worktree are excluded from the active-source lint scope. |
| Frontend production build | Passed | Route and vendor splitting reduced the entry application chunk to about 59 kB minified; the largest remaining chunk is about 390 kB minified. These are artifact sizes, not loading-time measurements. |
| Functions production build | Passed | TypeScript compilation on the hardened callable implementation. |
| Production dependency audit | Passed at high-severity gate | Root production audit: zero findings. Functions: seven moderate transitive findings; no high or critical findings. |
| GitHub Actions | Passed in the private source repository | Node.js 22 and Java 21 repeated clean installs, lint, tests, emulators, both builds, and audit gates in 1m 44s. The private run is not linked from this public case study. |

## Hosted observations

| Surface | Verified result |
| --- | --- |
| Firebase-hosted homepage | Rendered SocioHR Play interface. |
| Public catalogue | Rendered personality activity cards without login. |
| Activity introduction and first question | Opened without an account; no completion or submission performed. |
| Administrator URL, signed out | Redirected to the login page. This is not a backend authorization test. |
| Login | Rendered labeled email/password fields and links. No sign-in attempted. |
| Homepage content endpoint | HTTP 200 with JSON content type. |
| Register, password reset, terms, privacy | Direct HTTP 200 HTML checks; HTTP success alone does not prove interactive behavior. |
| Repository homepage metadata | Updated to the verified Firebase Hosting link; source visibility remains private. |
| GitHub professional profile | Public page returned HTTP 200 and the account was verified through GitHub CLI. |

The deployed build was not matched to the hardened local revision. The new backend rules and submission function were tested only in local emulators and have not been deployed. No live writes, email sends, account creation, administrative exports, or client-data changes were performed.

## Presentation fixes prepared locally

The local patch corrects administrator navigation, removes the space-consuming “Admin Panel” header text while retaining an accessible home link, and removes nested link/button controls. It adds navigation labels; keyboard-operable answer choices; text-answer and selected-state labels; Romanian document language and an original favicon; initial loading, unavailable-activity, public load/empty/error, incomplete-profile, dashboard-error and 404 recovery states. Durations are now described as estimates because no countdown is enforced.

Security changes restrict profile and image access, deny direct client submission writes, validate and score on the server, make retries idempotent, apply transactional limits and generate server-owned emails. These fixes are not deployed. App Check is deliberately deferred; the public callable relies on server validation and rate limits. A coordinated Firebase release, full screen-reader/cross-browser testing, protected-role QA and post-release synthetic submissions remain outstanding.

Local browser checks at a 390 × 844 viewport verified the public homepage layout, mobile menu expansion and collapse, catalogue navigation, the labeled activity return control, Space-key answer selection, and progression to the second question. The activity was then exited without completion or persistence. Local browsing read published client content; it was not an isolated demo backend. Error and empty-state branches were reviewed in source and compiled, but were not fault-injected against the live backend.

The original review build produced a single 1,529.26 kB JavaScript bundle (430.85 kB gzip). The hardened build splits routes and vendors: the application entry is about 59.22 kB (16.73 kB gzip), while the largest Firestore chunk is about 390.36 kB (111.70 kB gzip). CSS is about 114.43 kB (17.51 kB gzip). These are build artifact sizes, not measured loading times. Lint and `git diff --check` pass.

A static check matched all 58 literal internal Link destinations to declared routes; dynamic destinations still require workflow testing. The local 404 recovery page rendered, and the document reported Romanian language and the new favicon reference.

All 15 Markdown link/image references resolved to existing local targets or the two verified external destinations. Five Markdown files were parsed into a local HTML preview; the README and illustrations were visually reviewed. This is a local rendering check, not a published GitHub rendering check.
