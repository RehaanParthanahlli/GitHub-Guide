```bash
#######################################################################
# GIT · GITHUB · SSH · PAT — FULL FLOW WITH REAL COMMANDS
# (Everything you asked, consolidated, executable, no theory gaps)
#######################################################################

#######################################################################
# 0) GIT HAS NO LOGIN (LOCAL, OFFLINE)
#######################################################################
git status
git add .
git commit -m "local commit"
# ↑ No auth, no internet, no user check

#######################################################################
# 1) REMOTES — origin & upstream
#######################################################################
git remote -v

# add origin (your repo / fork)
git remote add origin git@github.com:RehaanParthanahlli/repo.git

# add upstream (original repo you forked from)
git remote add upstream git@github.com:ORIGINAL_OWNER/repo.git

#######################################################################
# 2) FETCH & MERGE (YOU NEVER MERGE INTO UPSTREAM)
#######################################################################
git fetch upstream
git merge upstream/main        # merges INTO LOCAL branch
git push origin main           # pushes to YOUR repo

#######################################################################
# 3) WHY `git push` IS ENOUGH
#######################################################################
git push                       # works because:
# - remote exists
# - branch has upstream
# - auth is already configured

# first time only:
git push -u origin main        # sets upstream, future `git push` works

#######################################################################
# 4) SSH — KEY-BASED AUTH (DAILY USE)
#######################################################################

# generate modern SSH key (DO THIS ONCE PER MACHINE)
ssh-keygen -t ed25519 -C "RehaanParthanahlli@GitHub"

# start ssh-agent
eval "$(ssh-agent -s)"

# add key to agent
ssh-add ~/.ssh/id_ed25519

# COPY THIS OUTPUT AND ADD TO GITHUB → Settings → SSH Keys
cat ~/.ssh/id_ed25519.pub

# TEST SSH (DEFINITIVE CHECK)
ssh -T git@github.com
# Expected:
# Hi RehaanParthanahlli! You've successfully authenticated...

#######################################################################
# 5) HOW SSH AUTH ACTUALLY WORKS
#######################################################################
# Git sees SSH URL → calls system SSH
git push
# internally:
# ssh git@github.com
# SSH uses PRIVATE KEY (never sent)
# GitHub maps PUBLIC KEY → RehaanParthanahlli
# Permission checked → push allowed

#######################################################################
# 6) HTTPS + PAT (TEMPORARY / CI / OTHER MACHINES)
#######################################################################

# switch remote to HTTPS (ONLY if you want to test PAT)
git remote set-url origin https://github.com/RehaanParthanahlli/repo.git

git push
# Username: RehaanParthanahlli
# Password: <PASTE PAT HERE>

#######################################################################
# 7) WHY GIT DOES NOT ASK AGAIN (CACHED PAT)
#######################################################################
git config --show-origin --get-all credential.helper
# likely shows Git Credential Manager or gh helper

# to FORCE Git to ask again (learning only)
git config --global --unset credential.helper
git push

#######################################################################
# 8) GITHUB CLI (gh) — SEPARATE FROM GIT
#######################################################################

# login (uses HTTPS token, NOT SSH)
gh auth login

# verify gh identity
gh auth status
gh api user                   # confirms who gh is authenticated as

# IMPORTANT:
# git → SSH
# gh  → HTTPS token
# This is NORMAL.

#######################################################################
# 9) USER VS USERNAME VS COMMIT AUTHOR
#######################################################################

# commit author (METADATA ONLY)
git config --global user.name "Any Name"
git config --global user.email "any@email.com"

# push identity (REAL AUTH)
# SSH → key → GitHub account
# HTTPS → PAT → GitHub account

#######################################################################
# 10) CHECK WHICH AUTH YOU ARE USING
#######################################################################
git remote -v
# git@github.com:...  → SSH
# https://github.com  → HTTPS + PAT

#######################################################################
# 11) OFFLINE VS ONLINE (FINAL TRUTH)
#######################################################################
# OFFLINE (no auth):
git add .
git commit -m "offline ok"

# ONLINE (auth required):
git fetch
git pull
git push
ssh -T git@github.com
gh api user

#######################################################################
# 12) SECURITY RULES (DO NOT BREAK)
#######################################################################
# ❌ Never share private key (~/.ssh/id_ed25519)
# ❌ Never commit PAT
# ✅ One SSH key per machine
# ✅ Short-lived PAT for temp use
# ✅ Revoke PAT after use

#######################################################################
# FINAL ONE-LINERS (LOCK THEM)
#######################################################################
# Git does not authenticate — remotes do
# SSH = remote connection, local secret
# PAT = HTTPS only, never with SSH
# origin/upstream are just names
# Silent git push = success
#######################################################################
```
