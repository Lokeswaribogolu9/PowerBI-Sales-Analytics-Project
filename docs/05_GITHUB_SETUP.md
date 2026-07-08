# 05 — Put This Project on GitHub (Complete Beginner Guide)

This guide assumes you've never used Git or GitHub before. Follow every step in order.

---

## Part 1: Create a GitHub Account (skip if you already have one)

1. Go to `https://github.com`.
2. Click **Sign up** (top right).
3. Enter your email, create a password, choose a username, and follow verification.
4. You'll land on your GitHub homepage.

---

## Part 2: Install Git on Your Computer

### Windows
1. Go to `https://git-scm.com/downloads` → click **Windows** → download starts automatically.
2. Open the `.exe` file → click **Next** through the installer with default options → **Install**.
3. Confirm it worked: open **Start Menu** → type `cmd` → Enter → type:
   ```
   git --version
   ```
   You should see something like `git version 2.44.0`.

### Mac
1. Open **Terminal** (`Cmd + Space`, type "Terminal", Enter).
2. Type:
   ```
   git --version
   ```
3. If not installed, macOS prompts you to install "Command Line Developer Tools" — click **Install**.

**Common mistake:** If `git --version` says "command not found," the install didn't finish — restart your terminal or computer and try again.

---

## Part 3: Create a New Repository on GitHub

1. Go to `https://github.com`, make sure you're logged in.
2. Click the **"+"** icon (top right) → **"New repository"**.
3. **Repository name**: `PowerBI-Sales-Analytics-Project`
4. **Description**: "Power BI sales analytics dashboard — star schema, DAX, RFM segmentation, sales rep leaderboard, returns analysis."
5. Choose **Public** (so recruiters can see it) or **Private**.
6. Leave all initialization checkboxes **unchecked** (you already have a README).
7. Click **"Create repository"**.
8. Copy the URL shown, e.g. `https://github.com/YOUR-USERNAME/PowerBI-Sales-Analytics-Project.git` — you'll need it next.

---

## Part 4: Upload This Project to GitHub

### Option A: GitHub Desktop (easiest — no typed commands)

1. Download from `https://desktop.github.com/`, install, sign in.
2. **File → Add Local Repository** → **Choose...** → select this project folder.
3. If prompted "This directory does not appear to be a Git repository," click **"create a repository"** in that message.
4. Confirm the name → **Create Repository**.
5. Type a summary like "Initial commit: Power BI sales analytics project" in the bottom-left box.
6. Click **"Commit to main"**.
7. Click **"Publish repository"** (top). Match the name to Part 3's repo. Uncheck "Keep this code private" if you want it public. Click **Publish Repository**.
8. Refresh your GitHub repo page — all files should now be there.

### Option B: Command Line

1. Open Terminal (Mac) or Command Prompt (Windows).
2. Navigate into the project folder:
   ```
   cd path/to/PowerBI-Sales-Analytics-Project
   ```
3. Run, one line at a time:
   ```
   git init
   git add .
   git commit -m "Initial commit: Power BI sales analytics project"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/PowerBI-Sales-Analytics-Project.git
   git push -u origin main
   ```
4. Replace `YOUR-USERNAME` with your actual GitHub username (copy the exact URL from Part 3).
5. If prompted for a password, GitHub requires a **Personal Access Token**, not your account password: GitHub → profile picture → **Settings** → **Developer settings** → **Personal access tokens** → **Tokens (classic)** → **Generate new token** → check `repo` → **Generate token** → paste it in place of your password.

**Common mistakes:**
- Wrong folder path in `cd` — use `ls` (Mac) or `dir` (Windows) to check your current folder.
- Forgetting to replace `YOUR-USERNAME` — causes "repository not found."
- Using your GitHub password instead of a Personal Access Token — will be rejected.

---

## Part 5: Update the Project Later

**GitHub Desktop:** open the app, review the auto-detected changes, type a commit message, **Commit to main**, then **Push origin**.

**Command line:**
```
git add .
git commit -m "Add finished dashboard and screenshots"
git push
```

---

## Note on the .pbix file

Power BI `.pbix` files can be large (50-200+ MB). GitHub's free tier allows up to 100 MB per file and warns above 50 MB. If yours is too large:
- In Power BI: **File → Options and settings → Options → Data Load**, turn off "Auto date/time" to shrink the file.
- Use **Git LFS** (Large File Storage) — see `https://git-lfs.com`.
- Or don't commit the `.pbix` at all — publish it to Power BI Service instead and link to it from your README.
