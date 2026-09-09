# Engineering notes

These notes summarize implementation inspected during September 5–9, 2026. They do not expose application source or operational identifiers. Historical reasons are stated only where supported by implementation comments, the CV, or the developer's confirmed contribution record; other observations explain how the technologies function in this application.

## Architecture and state

React Router separates public pages, an immersive activity player, and protected student/admin layouts. React Context shares the Firebase user and profile-derived role. Page-local hooks hold answers, question position, loading flags, editor fields, and submission state. Theme preference is persisted locally. No external global state or query-cache library was found in the application dependencies.

Service modules wrap Firestore reads and administration, image storage, homepage settings, and a callable submission function. TypeScript describes shared records; Zod validates activity and submission data at the server boundary. Frontend role checks shape navigation, while tested Firestore and Storage rules enforce data access. The diagram is conceptual and omits infrastructure names, locations, identifiers, and credentials.

## 1. Shared model, different activity behavior

Five activity types share questions, answers, optional outcomes, and publication settings. The player branches by type: assessments calculate points; personality activities count mapped outcomes; multiple-intelligences activities sum weighted outcomes; crosswords use a dedicated grid; questionnaires store responses without producing a score.

Assessment questions can award partial credit while subtracting incorrect selections. The score is bounded below at zero. The deployed hardened implementation sends answer selections to a Cloud Function that reloads the published activity and recomputes the result before persistence; client-supplied scores are rejected. Published activity data still reaches the browser, including answer mappings. These activities should not be presented as a secure examination or a validated hiring-decision system.

The shared model reduces repeated authoring concepts. The tradeoff is branching across format-specific behavior. Pure scoring functions now provide deterministic tie behavior and focused tests cover partial credit, malformed answers, authoring validation, and result selection. Configured duration is presented as an estimated completion time; an enforced countdown is not implemented.

## 2. Homepage content under restrictive networks

Homepage text can be edited by staff. The implementation tries a same-origin server endpoint, then a direct REST fallback, alongside a live Firestore subscription. Cached subscription events are ignored, and a four-second fallback releases the hero placeholder.

Comments and the CV connect this work to blocked Google API traffic at events. The implementation and a working JSON endpoint were verified; the original incident and claimed recovery at an event were not independently reproduced.

The tradeoff is more synchronization and fallback logic, including competing asynchronous reads. The resilience applies to homepage text, not all catalogue or activity requests. It depends on the Firebase Hosting rewrite being configured; the deployed same-origin endpoint passed the review check.

## 3. Guest results and contact separation

The guest email flow uses one server transaction to write completion information separately from a minimal participation contact record. The server validates input, applies transactional rate limits, recomputes the result, and creates an HMAC-bound receipt before requesting EmailJS delivery from managed secrets. Retrying the same operation returns the original result and does not send a second email. The result screen also offers a guest view that bypasses saving and email.

This separation reduces direct coupling of email addresses and answers in those guest records. It does not prove irreversible anonymity: shared titles and timestamps may allow correlation, and signed-in results and questionnaires have different identity handling. Email delivery occurs after the transaction; an ambiguous provider outcome is marked unconfirmed and is not retried automatically, avoiding duplicate sends at the cost of requiring operational follow-up. No legal or regulatory guarantee follows from the data layout.

## Supporting practices and remaining work

The editor validates required content and selected format-specific constraints before saving. Image handling rejects oversized inputs, resizes them, and encodes WebP before upload. Questionnaire CSV export quotes fields, preserves Romanian text, and includes a defense against leading spreadsheet-formula characters. These are source observations, not end-to-end delivery guarantees.

The deployed revision adds backend authorization and abuse controls, deterministic persistence, a green lint baseline, scoring and rules tests, route-level lazy loading, keyboard-operable choices, and clearer failure states. Remaining work includes protected-role browser QA, full assistive-technology testing, operational monitoring and approved retention/deletion procedures. The remaining items are limitations and recommendations, not delivered features or an agreed client roadmap.
