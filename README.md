# MediNest v0.4 — install guide (non-technical)

**What you are getting:** The existing medicine organiser with a new optional multi-photo cloud AI scanner. The medicine cabinet, profiles, schedules, export and local OCR remain available. The AI feature will **not** work until you create a free Cloudflare Worker and supply a Gemini API key as a secret. This is not an independently tested clinical product.

## A. Update your existing GitHub Pages app

1. **Back up** existing data from MediNest > Settings > Export backup, and store the JSON file securely. Updating GitHub files should not remove browser records, but a backup is essential.
2. Open your GitHub `medinest` repository, click **Add file > Upload files**. Upload only the **five files inside `medinest/`**: `index.html`, `icon.svg`, `manifest.webmanifest`, `sw.js`, `README.md`. Replace existing files (if GitHub's upload page will not replace them, open each file on GitHub, choose the pencil/Edit action, paste the new file content, and commit). Keep the files at the repository root, not inside an extra directory.
3. Commit the changes. Wait a few minutes and refresh `https://sgunathilake.github.io/medinest/`. Check that Settings has **Optional cloud AI scanner (v0.4)**.

## B. Set up your free Gemini API key

1. Go to https://aistudio.google.com/apikey and sign in.
2. Create an API key for the Gemini Developer API. Confirm **your account has a suitable free tier** and that the model `gemini-2.5-flash` is available. Free quotas and data handling terms change. **Do not add billing unless you want to**.
3. Copy the key privately. **Never paste it in GitHub, MediNest, chat, or a public screenshot.**

## C. Create a free Cloudflare Worker (browser only, no command line)

1. Sign up at https://dash.cloudflare.com/ (free plan). Go to **Workers & Pages > Create > Worker** (names can change slightly).
2. Give it a name such as `medinest-ai-private`, then deploy the starter Worker.
3. Open the Worker and choose **Edit code**. Replace the starter code with the **entire contents** of `cloudflare-worker/worker.js`, then **Deploy**.
4. Open **Settings > Variables and Secrets** and add **two Secrets** (type **Secret**, not plain text):
   - `GEMINI_API_KEY` = the API key from Google AI Studio.
   - `ACCESS_CODE` = a **long random private passphrase** that you create. This is *not* your Gemini key. Use 20+ random characters and keep it in a password manager.
5. Deploy/save secret changes. Copy your Worker's HTTPS address (like `https://medinest-ai-private.your-subdomain.workers.dev`). Do not add `/scan` to the address in MediNest.

**Security:** This Worker accepts requests from the specific GitHub Pages origin in the code, and requires your private access code. Origin checks are not authentication, and an access code is not enterprise-grade access control. Someone who obtains the code can use your AI quota. Never share it; rotate it if exposed. Set account-level spending limits/alerts where available and keep billing disabled for a strict zero-cost trial. For a wider audience, add robust authentication and rate limits first.

## D. Connect MediNest to the Worker

1. Open your MediNest website > **Settings**.
2. Paste the Worker address into **Cloudflare Worker HTTPS address**, click **Save AI address**.
3. Go to **Cabinet > Scan packaging**, choose **up to 6 photos of the same medicine**. Take front, ingredients/strength, quantity, and expiry label. Avoid names, addresses and prescription labels.
4. Enter your **private scanner access code** (not the Gemini API key), click **Analyse all photos with AI**, and approve sending the selected photos to Cloudflare/Google.
5. Review every proposed field and the evidence, then **Use verified details** to fill the normal medicine form. Select the correct household member, confirm *actual remaining quantity* (not just pack size), and save. Do not rely on AI-generated instructions.

## Important privacy and medical limits

- Photos are compressed **on your device**, then explicitly sent to your Cloudflare Worker and Google's Gemini API. They are not uploaded merely by selecting them. No photo storage is implemented by this app or Worker, but provider processing/retention and logging are governed by their terms and account settings.
- Other medicine records remain in **localStorage on that device**, not synced across devices. JSON backups are **unencrypted** and sensitive. Different browsers/devices have separate records.
- No medical-grade verification, dosage recommendations, interaction checking, reliable background notifications, or authentication between household profiles. Do not use this as your sole medication reminder.
- AI can confidently misread labels. If the expiry is printed as **month/year only**, the AI should leave the ISO date blank rather than invent a day. Enter it manually only when you know the appropriate interpretation from the packaging/manufacturer.
- A free Cloudflare Worker does **not** guarantee that Google Gemini usage is free. Check quotas and terms in your Google account before testing.
- If cloud AI setup is not complete, **Offline-style OCR** and manual entry are still available. OCR's first use needs an internet connection to fetch its library.

## Troubleshooting

- **Incorrect scanner access code**: check the `ACCESS_CODE` Worker Secret; don't use the Gemini API key in that field.
- **AI provider request failed (404)**: check whether `gemini-2.5-flash` is available for your account. Update the `MODEL` constant in `worker.js` to an available vision model, redeploy.
- **AI provider rate limit**: free quota may be exhausted; wait or use OCR.
- **Forbidden origin**: Worker is configured for `https://sgunathilake.github.io`. If you host elsewhere, change `ALLOWED_ORIGIN` to your exact site origin and redeploy.
- **Old version persists**: refresh, close and reopen the installed app; the updated service worker may need a second load. Export a backup before clearing any site data.
- **Cannot overwrite GitHub files with Upload files**: use GitHub's web editor on each file or use GitHub Desktop on a computer.
