# JumpSeat Signal Intelligence — Team Onboarding Guide
**Welcome to the skills system. Read this once, follow the steps, and you'll be running the pipeline in under 20 minutes.**

---

## What you're getting access to

This is a private GitHub repository with 10 Claude skills that run in sequence to analyze target accounts. You give it a company name and website. It gives you back a full intelligence dossier and two emails ready to send — in about 30 minutes.

The skills live on GitHub so that when we improve them, you get the update automatically without anyone having to send you a new file.

You have **two ways to use them:**

- **Option A — Claude Code (terminal):** More powerful. Runs skills as commands. Recommended if you're comfortable opening a terminal.
- **Option B — Claude Web (browser):** No installation required. Works today. Good starting point if Option A feels like too much right now.

Pick the one that works for you. You can always switch later.

---

---

# OPTION A — Claude Code + GitHub
### Recommended for the full experience

---

## Step 1 — Get access to GitHub (if you don't have an account)

GitHub is where the skills are stored. You need a free account to access the private repository.

1. Go to **github.com**
2. Click **"Sign up"** in the top right corner
3. Enter your work email and create a password
4. Verify your email address
5. Once your account is created, **send your GitHub username to Hareli** so she can add you to the private repository

Once Hareli adds you, you'll receive an email from GitHub with a subject like *"hareliortubia-design invited you to Jumpseat"* — click **Accept invitation** in that email.

---

## Step 2 — Install Git on your computer

Git is the program that lets you download and update the skills from GitHub. Check if you already have it before installing.

### On Mac:

1. Open the **Terminal** app
   - Press `Command + Space`, type `Terminal`, hit Enter
2. Type this command and press Enter:
   ```
   git --version
   ```
3. **If you see something like** `git version 2.39.0` — Git is already installed. Skip to Step 3.
4. **If you see a popup** asking to install developer tools — click **Install** and wait for it to finish. This is the official Apple installation, it's safe.
5. Once installed, run `git --version` again to confirm it worked.

### On Windows:

1. Go to **git-scm.com/download/win**
2. The download will start automatically — run the installer
3. Click **Next** through all screens, keeping all default options
4. When the installer finishes, open **Git Bash** (you'll find it in your Start menu)
5. Type `git --version` and press Enter to confirm it's installed

---

## Step 3 — Install Claude Code

Claude Code is the desktop/terminal app for Claude that lets you run skills as slash commands.

1. Go to **claude.ai/download**
2. Download the version for your operating system (Mac or Windows)
3. Open the downloaded file and follow the installation steps
4. Launch Claude Code
5. Sign in with your Anthropic / Claude account
   - If you don't have a Claude account yet, create one at **claude.ai** — you'll need a paid plan (Pro or above) to use the full capabilities

---

## Step 4 — Clone the repository (download the skills)

"Cloning" means downloading a copy of the GitHub repository to your computer. You only do this once.

### On Mac (Terminal) or Windows (Git Bash):

1. Open Terminal (Mac) or Git Bash (Windows)
2. Decide where you want the skills folder to live on your computer. For example, your Documents folder:
   ```
   cd ~/Documents
   ```
   *(Type this and press Enter — it navigates you to your Documents folder)*

3. Clone the repository by running:
   ```
   git clone https://github.com/hareliortubia-design/Jumpseat.git
   ```
   GitHub will ask for your username and password.
   
   > **Note on password:** GitHub no longer accepts your regular password here. You need a **Personal Access Token**. Here's how to get one:
   > 1. Go to github.com → click your profile picture (top right) → **Settings**
   > 2. Scroll down to **Developer settings** (bottom of left sidebar)
   > 3. Click **Personal access tokens** → **Tokens (classic)**
   > 4. Click **Generate new token (classic)**
   > 5. Give it a name (e.g. "JumpSeat"), set expiration to **90 days**, check the **repo** checkbox
   > 6. Click **Generate token** — copy the token immediately (you won't see it again)
   > 7. Use this token as your password when Git asks

4. Once cloned, navigate into the folder:
   ```
   cd Jumpseat
   ```

5. Confirm the skills are there:
   ```
   ls skills/base/
   ```
   You should see 10 `.md` files — one for each skill.

---

## Step 5 — Open Claude Code in the skills folder

1. Open Claude Code (the app you installed in Step 3)
2. You need to point it to the Jumpseat folder. In Claude Code, open the folder:
   - On Mac: `File → Open Folder` → navigate to `Documents/Jumpseat`
   - Or from Terminal, while inside the Jumpseat folder, type:
     ```
     claude
     ```
     This opens Claude Code directly in that folder.

---

## Step 6 — Run your first skill

Inside Claude Code, type this in the chat and press Enter:

```
/skill-jumpseat-kickoff
```

Claude will read the skill file and start the pipeline orchestrator. It will ask you what you have in hand and route you to the right next step.

**To analyze a specific company from scratch, the full sequence is:**

```
1. /skill-jumpseat-kickoff
   → Tell it: "I have a company name and website, nothing else"
   → It routes you to skill-deep-research

2. /skill-deep-research
   → Give it: company name + website URL
   → It produces: research-brief.json

3. /skill-icp-fit-scoring
   → Give it: the research brief + client name (e.g. ISCO)
   → It produces: Tier 1 / Tier 2 / Not ICP with a score

4. /skill-signal-research
   → Only if the account passed the ICP gate
   → It produces: signal matrix (C / I / N / U)

5. /skill-conviction-algorithm
   → It produces: conviction score + priority level

6. /skill-dossier-builder        ← only for HIGH conviction accounts
   /skill-contact-mapping
   /skill-email-personalization

7. /skill-salesforce-export
   → Produces: CSV ready to import to your CRM
```

Each skill tells you exactly what to run next at the end — look for the **CASCADE → Next step** section in every output.

---

## Step 7 — Keep your skills up to date

When Hareli publishes an improvement to a skill, you get it in seconds:

1. Open Terminal or Git Bash
2. Navigate to your Jumpseat folder:
   ```
   cd ~/Documents/Jumpseat
   ```
3. Pull the latest changes:
   ```
   git pull
   ```

That's it. Your skills are now up to date.

---

---

# OPTION B — Claude Web (browser only)
### No installation required — start today

---

## Step 1 — Download the skills from GitHub

Even if you're not using the terminal, you still need to download the skill files from GitHub.

1. Make sure Hareli has given you access to the repository (you'll have received an invitation email from GitHub — accept it)
2. Go to: **github.com/hareliortubia-design/Jumpseat**
3. Click the green **"Code"** button (top right of the file list)
4. Click **"Download ZIP"**
5. A file called `Jumpseat-main.zip` will download to your computer
6. Open (unzip) that file — you'll get a folder called `Jumpseat-main`
7. Inside it, find the folder `skills/base/` — this contains the 10 skill files you need

---

## Step 2 — Create a Claude Project

Claude Projects let you upload files that Claude keeps in context permanently — so you don't have to paste anything every time.

1. Go to **claude.ai** and sign in
2. In the left sidebar, click **"New Project"**
3. Name it: **JumpSeat Signal Intelligence**
4. You'll see a section called **"Project Knowledge"** on the right side

---

## Step 3 — Upload the skills to your Project

1. Inside your Project, click **"Add content"** or the upload icon in Project Knowledge
2. Upload **all 10 files** from the `skills/base/` folder:
   - `skill-jumpseat-kickoff.md`
   - `skill-icp-onboarding.md`
   - `skill-signal-hunter.md`
   - `skill-deep-research.md`
   - `skill-icp-fit-scoring.md`
   - `skill-signal-research.md`
   - `skill-conviction-algorithm.md`
   - `skill-dossier-builder.md`
   - `skill-contact-mapping.md`
   - `skill-email-personalization.md`
   - `skill-email-personalization.md`
   - `skill-salesforce-export.md`

3. Also upload the `client-profile.yaml` file for the client you're working with (Hareli will send you this separately — it's not in the public repo for privacy reasons)

Once all files are uploaded, **every conversation inside this Project will have access to all the skills** without you having to paste anything.

---

## Step 4 — Run your first skill

Inside your JumpSeat Project, start a new conversation and type:

```
Run skill-jumpseat-kickoff
```

Or if you already know what you want to do:

```
Run skill-deep-research for company: Freeport-McMoRan, website: fcx.com
```

Claude will read the skill from the Project Knowledge and execute it step by step.

> **Model recommendation:** Use **Claude Opus 4** for research and dossier tasks — it produces significantly better output on complex intelligence work. You can select the model in the top left of the Claude chat window.

---

## Step 5 — Getting updated skills (Option B)

When Hareli updates a skill, you'll need to:
1. Download the new ZIP from GitHub (same process as Step 1)
2. Go to your Claude Project → Project Knowledge
3. Delete the old version of the updated skill
4. Upload the new version

This is why Option A (Claude Code + git pull) is more efficient at scale — updates happen in one command. But Option B works perfectly well to get started.

---

---

# Quick reference — what each skill does

| Skill | When to run | What it produces |
|-------|-------------|-----------------|
| `skill-jumpseat-kickoff` | Always first if you're not sure where to start | Routes you to the right skill |
| `skill-icp-onboarding` | New client setup — once per client | `client-profile.yaml` |
| `skill-signal-hunter` | After onboarding — workshop with client | Signal candidate list |
| `skill-deep-research` | For every new account | `research-brief.json` |
| `skill-icp-fit-scoring` | After research brief | Tier 1 / Tier 2 / Not ICP |
| `skill-signal-research` | Tier 1 and Tier 2 accounts only | Signal matrix C/I/N/U |
| `skill-conviction-algorithm` | After signal research | Conviction score + priority |
| `skill-dossier-builder` | HIGH conviction accounts only | 4–5 page intelligence dossier |
| `skill-contact-mapping` | After dossier | Prioritized contact list with angles |
| `skill-email-personalization` | After contact map | Email 1 (≤75 words) + Email 2 (≤35 words) |
| `skill-salesforce-export` | End of pipeline | CSV for CRM import |

---

# The one rule you need to remember

**Never skip ICP scoring.** The gate exists so you don't spend 2 hours researching an account that should have been filtered out in 5 minutes.

```
TAM list → ICP scoring → Tier 1 / Tier 2 get signal research → HIGH conviction gets dossier + emails
```

Everything else follows from that.

---

# Questions?

Reach out to Hareli — hareliortubia@gmail.com

Repository: github.com/hareliortubia-design/Jumpseat
