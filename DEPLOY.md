# DEPLOY.md — atana.lat via GitHub Pages

> Step-by-step to take `atana.lat` from "registered domain" to "live site" in ~10 minutes once the domain is yours.

---

## Prerequisites

- [ ] `atana.lat` registered at a registrar that lets you edit DNS (Cloudflare Registrar recommended, US$ ~12).
- [ ] GitHub account (`@joaoroque`-style handle) — you have this.
- [ ] Local git installed; you've cloned at least one repo before.

---

## Step 1 — Create the GitHub repo  (2 min)

1. Go to <https://github.com/new>.
2. **Repository name:** `atana-site` (or `atana-lat` — your call; the name is invisible to visitors).
3. **Visibility:** Public.
4. **Initialize:** leave all checkboxes unchecked (you'll push the prepared content).
5. Click *Create repository*.

You'll see the empty repo with the "Quick setup" page.

---

## Step 2 — Push the prepared content  (3–10 min)

**Critical:** the `cd` path below is the actual path on João's Mac. Don't paste a `/path/to/...` placeholder — use the real path with proper quotes (the folder name contains spaces and a hyphen).

### Step 2a — Open a terminal and go to the deploy folder

```bash
cd "/Users/joaoroque/Documents/Cultural production - book/Dados da Economia Cultural no Brasil/_deploy_atana_lat"

# Sanity check — confirm you're in the right place
pwd      # should print the path above
ls -la   # should show: index.html, CNAME, .nojekyll, 404.html, README.md, DEPLOY.md, the PDF
```

If `pwd` shows anything other than the deploy folder, **stop here** and re-check the path before running git commands. Initializing git in the wrong folder (e.g., your home directory) creates a stray `.git` you'll have to clean up later.

### Step 2b — Initialize the local repo

```bash
git init
git branch -M main
git add .
git commit -m "Initial Atana Index Vol. 1 deploy"
```

### Step 2c — Pause and create the GitHub repo first

Before adding the remote, go to <https://github.com/new> in your browser:
- Repository name: `atana-site`
- Visibility: Public
- **Leave all "Initialize this repository with" boxes UNCHECKED** (if GitHub creates a README, you'll get a merge conflict on first push)
- Click *Create repository*.

You should now have an empty repo at `https://github.com/joaoroquedasilvajunior/atana-site`. Return to the terminal.

### Step 2d — Add the remote and push

Pick **one** URL form. The SSH form starts with `[email protected]:`; the HTTPS form starts with `https://github.com/`. Don't combine them — `git@https://...` is malformed and will fail.

**SSH form** (silent push if you have an SSH key on GitHub):
```bash
git remote add origin [email protected]:joaoroquedasilvajunior/atana-site.git
git push -u origin main
```

**HTTPS form** (prompts for credentials; needs a Personal Access Token, not a password):
```bash
git remote add origin https://github.com/joaoroquedasilvajunior/atana-site.git
git push -u origin main
```

If you've added the wrong remote already, fix it before pushing:
```bash
git remote remove origin
git remote add origin [email protected]:joaoroquedasilvajunior/atana-site.git
```

### If you don't have an SSH key on GitHub yet

```bash
# Generate one
ssh-keygen -t ed25519 -C "joaoroquer@gmail.com"
# Press Enter through all prompts (default location, no passphrase if you want frictionless pushes)

# Copy the public key to clipboard
cat ~/.ssh/id_ed25519.pub | pbcopy

# Add it to GitHub: https://github.com/settings/keys → New SSH key → paste → Save

# Verify
ssh -T [email protected]
# Should print: "Hi joaoroquedasilvajunior! You've successfully authenticated, but GitHub does not provide shell access."
```

### After successful push

The GitHub repo should now show seven items: `index.html`, `Atana_Index_Vol1_Two_Creative_Time_Zones.pdf`, `CNAME`, `.nojekyll`, `404.html`, `README.md`, `DEPLOY.md`.

---

## Step 3 — Enable GitHub Pages  (1 min)

1. In the repo, go to **Settings → Pages** (left sidebar).
2. **Source:** *Deploy from a branch*.
3. **Branch:** `main` · folder: `/ (root)`.
4. Click **Save**.

GitHub will start building. After ~2 minutes the Pages section will show:

> Your site is live at https://joaoroque.github.io/atana-site/

Click it. The Atana Index should load. **Verify the "Download full PDF" button works** — this confirms the relative-path link to the PDF is correct.

---

## Step 4 — Add the custom domain  (2 min in GitHub + DNS in step 5)

Still in **Settings → Pages**:

1. **Custom domain:** type `atana.lat` → Save.
2. GitHub will warn that DNS isn't configured yet — that's expected; configure it in step 5.
3. Once DNS resolves correctly, GitHub will automatically issue an SSL certificate. Tick **Enforce HTTPS** as soon as it becomes available (usually within an hour of DNS pointing).

GitHub already detects the `CNAME` file in the repo and confirms the domain config matches.

---

## Step 5 — Configure DNS at your registrar  (3 min + propagation)

The fastest setup uses an apex domain (`atana.lat`) plus a `www` redirect.

### If you registered at Cloudflare

Open Cloudflare → atana.lat → **DNS → Records**. Add five records:

| Type | Name | Content | Proxy status |
|------|------|---------|--------------|
| A | `@` | `185.199.108.153` | DNS only (grey cloud) |
| A | `@` | `185.199.109.153` | DNS only (grey cloud) |
| A | `@` | `185.199.110.153` | DNS only (grey cloud) |
| A | `@` | `185.199.111.153` | DNS only (grey cloud) |
| CNAME | `www` | `joaoroque.github.io` | DNS only (grey cloud) |

**Important: set Proxy status to "DNS only" (grey cloud) for these initial records.** GitHub Pages and the orange-cloud Cloudflare proxy do not play well together with the default SSL handshake. You can turn on the proxy later once everything is stable.

### If you registered elsewhere

Same A records (`185.199.108.153` → `.111.153`) for the apex; same CNAME `www` → `joaoroque.github.io`. Most registrars have a DNS editor that takes the same input.

---

## Step 6 — Wait for DNS, then verify  (5–30 min)

DNS propagation can take anywhere from 1 minute to half an hour. To check progress:

```bash
dig atana.lat
# Should eventually return the four 185.199.108-111.153 IPs.
```

Or use <https://dnschecker.org/#A/atana.lat>.

Once DNS is propagated:

1. Open <https://atana.lat> in incognito mode.
2. The Atana Index should load.
3. Click "Download full PDF" — should download the file.
4. Test on mobile (phone with Wi-Fi off and 4G/5G on, to bypass router DNS cache).
5. Go back to GitHub → Settings → Pages → tick **Enforce HTTPS** if available.

---

## Step 7 — Add email forwarding  (5 min, optional)

If you registered at Cloudflare:

1. Open Cloudflare → atana.lat → **Email → Email Routing**.
2. Enable Email Routing (follow the wizard; it adds MX and TXT records automatically).
3. Add three forwarders:
   - `joao@atana.lat` → `joaoroquer@gmail.com`
   - `hola@atana.lat` → `joaoroquer@gmail.com`
   - `contact@atana.lat` → `joaoroquer@gmail.com`
4. Verify each by sending yourself a test email.

Now your email signature's `joao@atana.lat` actually works.

---

## Step 8 — Update Gmail to send AS `joao@atana.lat`  (10 min, optional but recommended)

Cloudflare Email Routing handles **inbound** only — by default you still send from `joaoroquer@gmail.com`. To send *as* `joao@atana.lat`:

1. Gmail → Settings → **Accounts and Import** → Send mail as → **Add another email address**.
2. Name: `João Roque`
3. Email: `joao@atana.lat`
4. Treat as alias: yes.
5. SMTP server: `smtp.gmail.com`, port 587, username `joaoroquer@gmail.com`, password = a Google App Password (generate one at <https://myaccount.google.com/apppasswords>).
6. Gmail will send a verification email to `joao@atana.lat` — Cloudflare forwards it to your Gmail; click the link.

Now you can compose emails *from* `joao@atana.lat` in Gmail. This is the v0-good-enough professional setup. Migrating to Workspace/Fastmail/ProtonMail can wait.

---

## Updating the site later

1. Edit `index.html` (or replace it with a regenerated version of `Atana_Index_Vol1.html`).
2. `git add -A && git commit -m "Update: [what changed]" && git push`.
3. GitHub Pages rebuilds in ~30 seconds.

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| `atana.lat` shows GitHub 404 page after DNS resolves | CNAME file missing or wrong | Verify `CNAME` file in the repo contains exactly `atana.lat` |
| "Domain's DNS record could not be retrieved" in GitHub | DNS not propagated yet | Wait 15 min, refresh |
| HTTPS not available after a few hours | Cloudflare proxy is ON | Turn off the proxy (grey cloud) on the A records |
| Site loads but PDF download fails | Filename mismatch | Verify the PDF file in the repo is exactly `Atana_Index_Vol1_Two_Creative_Time_Zones.pdf` (case-sensitive) |
| Charts not rendering inside the Index page | Browser cache | Hard refresh (Cmd+Shift+R) |

---

_Total time, end to end: ~15–25 minutes once domain is registered. Then update Gmail signature and you're at the readiness floor described in `../_onboarding/05_public_footprint_v0.md`._
