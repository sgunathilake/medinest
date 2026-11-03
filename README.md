# MediNest v0.1 — free, local-first PWA

## Included
- Household profiles (not access-controlled)
- Medicine inventory: strength, quantity, expiry, storage, instructions, notes
- Search and filter by person
- Daily scheduled-dose checklist and recent recorded history
- Expiry and low-stock dashboard indicators
- JSON export and restore
- Offline cache after first successful visit, installable PWA
- No external API, trackers, backend or subscriptions

## Launch free
1. Create a free GitHub account and repository. Upload `index.html`, `manifest.webmanifest`, `sw.js`, and `icon.svg` to the repository root.
2. In repository Settings → Pages, select Deploy from a branch, main, /root. Wait for the GitHub Pages URL to become available. GitHub Pages is public hosting: the *app code* is public, but the medication data is kept locally on each device, not uploaded to GitHub. Use a private repository with another compatible free static host if you don't want public source code.
3. Open the HTTPS site in a browser. On iPhone Safari, use Share → Add to Home Screen. On desktop Chromium browsers, use Install App where offered. Android Chrome supports Add to Home screen / Install.
4. Add a person, then add medicines. Export a backup after entering your information.

For a quick desktop preview, open `index.html` directly. Offline installation/service workers require HTTPS or localhost.

## Important limits
- No real push notifications; opening the app is required to see the checklist.
- No automatic multi-device sync. Each browser/device holds a separate database. You can transfer backups manually, but restoring replaces all data rather than merging.
- No login, encryption at rest, or profile access controls. Avoid using shared or untrusted devices. JSON backups are readable plain text; store securely.
- No automatic stock deduction from taken doses. Counts are manually maintained.
- A scheduled time is not a clinically validated reminder. There is no medical interaction or dose validation.
- LocalStorage can be wiped by clearing browser data or storage eviction. Keep regular backups.
- No barcode/photo scan yet.

## Next engineering milestones
1. IndexedDB storage and schema migrations, automated tests, validation, and accessibility audit.
2. Optional per-profile PIN and client-side encryption (with careful key recovery design).
3. Device-local notifications where supported, plus fallback calendar export; never claim guaranteed alerts.
4. Verified NHS medication references and label OCR with manual review.
5. Optional encrypted cloud sync with secure account authentication and UK GDPR/privacy review before sharing data across devices.

## Safety
MediNest is an organisational aid only. It does not recommend dosing, diagnose, or advise on missed doses, and must not replace prescription instructions, pharmacist guidance or clinical advice. Seek urgent medical help for emergencies.


## v0.2 photo scanning

From Dashboard or Cabinet, choose **Scan packaging**. Select up to six photos (12 MB each), extract text, review suggested medicine name, strength, quantity and expiry, then use the reviewed fields in the medicine form. The original images are not saved or uploaded by MediNest. OCR runs locally in the browser after downloading Tesseract.js v5.1.1 and its English recognition model from third-party CDNs on first use. Internet access is required for that first load, and subsequent availability depends on browser caching. The OCR library is not bundled and its CDN dependencies are not guaranteed to work offline.

**Important:** OCR is fallible, especially with medicine names, dates and small text. Expiry detection currently recognises month/year formats such as EXP 06/2027, interpreting the expiry as the last day of the month; other formats may not be recognised. Never rely on scanning for dosing or medical decisions. Review every value against the packaging before saving. No automatic dose instructions or medicine identification from image appearance.
